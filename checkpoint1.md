📋 Sobre o Projeto
Este projeto demonstra como implementar navegação entre múltiplas telas em um aplicativo Android utilizando Jetpack Compose e Navigation Compose, com foco na passagem de parâmetros obrigatórios e opcionais entre telas.
O projeto foi evoluído de forma incremental, commit a commit, partindo de uma estrutura base já existente com navegação simples e expandindo com passagem de dados entre telas.

🧭 Estrutura das Telas
app/
 └── src/
      └── main/
           └── java/
                └── carreiras/com/github/navigation_between_screens/
                     ├── MainActivity.kt
                     └── screens/
                          ├── LoginScreen.kt
                          ├── MenuScreen.kt
                          ├── PerfilScreen.kt
                          └── PedidosScreen.kt

🔄 Histórico de Evoluções — Commits

✅ Commit 1 — Passagem de parâmetros obrigatórios na tela de Perfil
Arquivos alterados: MainActivity.kt, MenuScreen.kt, PerfilScreen.kt
O que foi implementado
Neste commit, a rota da tela de Perfil foi modificada para exigir um parâmetro obrigatório: o nome do usuário. Antes, a rota era simplesmente "perfil" e não recebia nenhum dado. Agora, a rota passou a ser "perfil/{nome}", onde {nome} é um espaço reservado que precisa ser preenchido obrigatoriamente no momento da navegação.
Explicação técnica por arquivo
MainActivity.kt
A rota foi alterada de "perfil" para "perfil/{nome}". Dentro do bloco do composable, o valor do parâmetro é recuperado com it.arguments?.getString("nome", "Usuário Genérico"). O it representa o backStackEntry, que é o objeto que carrega os dados da navegação. O ?. é o operador de acesso seguro do Kotlin — garante que, se arguments for nulo, o código não quebra. O segundo argumento da função getString é o valor padrão caso nenhum dado seja recebido. O valor recuperado é armazenado em uma variável nome: String? — o ? indica que pode ser nula. Na chamada da PerfilScreen, o nome!! usa o operador "not-null assertion" do Kotlin, que força o uso do valor garantindo que ele não é nulo.
MenuScreen.kt
O botão de navegação para o Perfil foi atualizado de navController.navigate("perfil") para navController.navigate("perfil/Fulano de Tal"). Isso significa que o valor "Fulano de Tal" será inserido no lugar de {nome} na rota, e chegará como parâmetro na tela de destino.
PerfilScreen.kt
A função PerfilScreen foi atualizada para receber um terceiro parâmetro: nome: String. Por ser do tipo String sem ?, é um parâmetro obrigatório e não pode ser nulo. A exibição do texto foi alterada de "PERFIL" para "PERFIL - $nome", usando a interpolação de string do Kotlin — o $nome insere o valor da variável diretamente no texto, resultando em algo como "PERFIL - Fulano de Tal" na tela.

✅ Commit 2 — Passagem de parâmetros opcionais na tela de Pedidos
Arquivos alterados: MainActivity.kt, PedidosScreen.kt
O que foi implementado
Neste commit, a tela de Pedidos foi configurada para receber um parâmetro opcional: o cliente. A diferença em relação ao commit anterior é o uso de query string na rota — o formato ?chave={valor} — que é a abordagem do Navigation Compose para parâmetros que não são obrigatórios. Se a navegação ocorrer sem passar o parâmetro, um valor padrão é utilizado.
Explicação técnica por arquivo
MainActivity.kt
Foi adicionado o import androidx.navigation.navArgument, necessário para declarar parâmetros opcionais com configurações específicas. Sem esse import o código não compila.
A rota de Pedidos foi alterada de "pedidos" para "pedidos?cliente={cliente}". O ? separa o caminho da rota dos parâmetros opcionais, seguindo o padrão de query string. Além disso, foi adicionada a propriedade arguments, que recebe uma lista com a declaração de cada parâmetro opcional via navArgument. Dentro do navArgument("cliente") é definido o defaultValue = "Cliente Genérico" — esse é o valor que será usado quando ninguém passar o parâmetro cliente na navegação, tornando-o de fato opcional. O valor é recuperado na chamada com it.arguments?.getString("cliente") e passado diretamente para a PedidosScreen.
PedidosScreen.kt
A função PedidosScreen recebeu um novo parâmetro: cliente: String?. O ? indica que é nullable — pode receber nulo sem quebrar a aplicação, o que faz sentido para um parâmetro opcional. O texto exibido foi alterado de "PEDIDOS" para "PEDIDOS - $cliente", usando interpolação de string para exibir o valor recebido dinamicamente.

✅ Commit 3 — Inserção de valor ao parâmetro opcional na tela de Pedidos
Arquivo alterado: MenuScreen.kt
O que foi implementado
Neste commit, o botão de navegação para Pedidos no Menu passou a enviar um valor real para o parâmetro opcional cliente, criado no commit anterior. Antes, a navegação ia para "pedidos" sem passar nenhum dado, o que fazia a tela usar o defaultValue. Agora, um valor concreto é enviado.
Explicação técnica por arquivo
MenuScreen.kt
A linha de navegação foi alterada de navController.navigate("pedidos") para navController.navigate("pedidos?cliente=Cliente XPTO"). Seguindo o formato de query string, o valor "Cliente XPTO" é passado como o parâmetro cliente. Esse valor chega na PedidosScreen como String? e é exibido na tela como "PEDIDOS - Cliente XPTO". Sem essa mudança, a tela continuaria mostrando "PEDIDOS - Cliente Genérico" (o valor padrão do commit 2).

✅ Commit 4 — Passagem de múltiplos parâmetros
Arquivos alterados: MainActivity.kt, MenuScreen.kt, PerfilScreen.kt
O que foi implementado
Neste commit, a rota do Perfil foi expandida para receber dois parâmetros obrigatórios: nome e idade. Isso demonstra que é possível encadear múltiplos parâmetros em uma mesma rota, separados por /. Cada parâmetro foi declarado com seu respectivo tipo usando navArgument e NavType.
Explicação técnica por arquivo
MainActivity.kt
A rota foi atualizada de "perfil/{nome}" para "perfil/{nome}/{idade}" — os dois parâmetros ficam na rota separados por /, e a ordem importa: quem navegar precisa respeitar essa sequência.
Foi adicionada a propriedade arguments com uma lista de dois navArgument:

navArgument("nome") { type = NavType.StringType } — declara que nome é do tipo texto.
navArgument("idade") { type = NavType.IntType } — declara que idade é do tipo inteiro. O Navigation Compose faz a conversão automaticamente.

O valor de idade é recuperado com it.arguments?.getInt("idade", 0) — note o uso de getInt em vez de getString, pois o tipo declarado é NavType.IntType. O 0 é o valor padrão caso o argumento venha nulo. Na chamada da PerfilScreen, o idade!! passa o valor com o operador not-null, assim como foi feito com nome!!.
MenuScreen.kt
A navegação foi atualizada de navController.navigate("perfil/Fulano de Tal") para navController.navigate("perfil/Fulano de Tal/27"). O valor 27 é adicionado ao final seguindo a ordem da rota {nome}/{idade}.
PerfilScreen.kt
A função PerfilScreen recebeu o novo parâmetro idade: Int — sem ?, ou seja, obrigatório e não nulo. O parâmetro nome: String recebeu uma vírgula ao final pois agora há um parâmetro depois dele. O texto foi atualizado de "PERFIL - $nome" para "PERFIL - $nome tem $idade anos", exibindo os dois valores na tela com interpolação de string. O resultado final na tela será algo como "PERFIL - Fulano de Tal tem 27 anos".
