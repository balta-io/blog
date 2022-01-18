Priority Queue ou fila prioritária é um tipo de lista inclusa no C# 10 que permite que seus itens sejam ordenados por uma prioridade previamente definida

## Priority Queue
O **Priority Queue** funciona como uma fila normal no C#, onde usamos `Enqueue` para adicionar um item e `Dequeue` para removêlo, porém neste caso podemos informar uma prioridade.

Desta forma, ao desenfileirar os itens, esta prioridade será considerada como fonte primária de ordenação.

```csharp
var queue = new PriorityQueue<string, int>();
```

Neste caso, temos uma fila que conterá itens do tipo `string` e sua ordenação (Menor para maior) será dada por um inteiro (`int`).

### Adicionando itens a fila
Para adicionar itens a fila, podemos utilizar o método `Enqueue` que agora conta com dois argumentos, uma `string` que define o item que será enfileirado e um `int` que define a prioridade.

```csharp
queue.Enqueue("Item 1", 0);
queue.Enqueue("Item 2", 4);
queue.Enqueue("Item 3", 2);
queue.Enqueue("Item 4", 1);
```

### Percorrendo a fila
Para percorrer esta fila tomando como base a prioridade (`int`) que definimos previamente, podemos utilizar o método `TryDequeue`, que resultará em duas variáveis, sendo `item` do tipo `stirng`  e `priority` do tipo `int`.

> **IMPORTANTE** A ordem de desenfileiramento será feita automaticamente do menor para o maior, mais adiante neste artigo veremos como mudar isto.

```csharp
while (queue.TryDequeue(out var item, out var priority)) 
    Console.WriteLine($"Item: {item}. Prioridade: {priority}");
```

#### Resultado da execução
```
Item: Item 1. Prioridade: 0
Item: Item 4. Prioridade: 1
Item: Item 3. Prioridade: 2
Item: Item 2. Prioridade: 4
``` 

## Customizando a prioridade
No cenário acima, utilizamos a ordenação padrão aplicada pelo .NET/C#, que neste caso considera sempre do menor para o maior.

Como definimos nossa prioridade como um **inteiro** (`int`), os itens foram ordenados de 0 a 4.

```csharp
var queue = new PriorityQueue<string, int>(); // INT aqui é tipo da prioridade
```

### Ordenando por Strings
Embora a ordenação via **inteiros** pareça uma boa opção, há cenários onde queremos ordernar por cadeias de caracteres. Vamos tomar como base um usuário do sistema que pode ter diferentes perfis.

Neste cenário, os perfis podem ser **Admin** que tem privilégios totais, **Premium** que tem acesso à todos os cursos e **Aluno** que é um usuário regular.

Vamos começar definindo nosso usuário como um novo **Record**, apenas para intuito de demonstração.

```csharp
public record Student(string Name);
```

### Comparadores customizados
Um **Priority Queue** se baseia em um comparador para realizar a ordenação dos itens, e este ordenador nada mais é do que uma classe que implementa a interface `IComparer<T>`.

Sendo assim, podemos criar um comparador chamado `RoleComparer` que recebe um perfile (Role) do tipo `string` e o compara com outro perfil (Role), também do tipo `string`.

Ao implementar a interface `IComparer<T>` precisamos definir seu tipo, que neste caso será `string`, afinal o perfil aqui é apenas uma `string`. Porém nada impede que `T` seja um tipo complexo, como uma entidade `Role` por exemplo.

Obrigatoriamente, teremos um método chamado `Compare` que receberá dois objetos do mesmo tipo e deverá retornar o seguinte:
* 0: Os itens são iguais
* -1: A é maior que B
* 1: A é menor que B

Como resultado temos o seguinte trecho de código, apenas para intuito de demonstração do **Priority Queue**.

```csharp
public class RoleComparer : IComparer<string>
{
    public int Compare(string? roleA, string? roleB)
    {
        if (roleA == roleB)
            return 0;

        return roleA switch
        {
            "admin" => -1,
            "premium" => -1,
            _ => 1
        };
    }
}
```

### Utilizando um comparador customizado
Agora que criamos um comparador customizado, podemos dizer a nossa fila que o mesmo deve ser utilizado, informando uma instância do mesmo no construtor.

```csharp
var queue = new PriorityQueue<Student, string>(new RoleComparer());
```

Note que neste exemplo temos uma **Priority Queue** de `Student` e suas prioridades são do tipo `string`. Isto ocorre pois assumimos que os perfis (Role) são do tipo `string` para intuito desta demonstração.

Com a fila definida, podemos enfilieirar e desenfilerar os itens utilizando as prioridades definidas.

```csharp
queue.Enqueue(new Student("Spiderman"), "student");
queue.Enqueue(new Student("Ironman"), "premium");
queue.Enqueue(new Student("Batman"), "admin");

while (queue.TryDequeue(out var item, out var priority))
    Console.WriteLine($"Aluno: {item.Name}. Prioridade: {priority}");
```

Como resultado, teremos os itens sendo exibidos conforme definimos na classe `RoleComparer` onde **admin** é mais prioritário que **premium**, que por sua vez é mais prioritário do que **student**.

#### Resultado da execução
```
Aluno: Batman. Prioridade: admin
Aluno: Ironman. Prioridade: premium
Aluno: Spiderman. Prioridade: student
```

## Conclusão
O C# possui um enorme leque de formas para se trabalhar com listas e filas. É muito comum trabalharmos com `List`, `Enumerable` e esquecermos de outros tipos como o `PriorityQueue`, porém conhecer bem estes tipos pode aumentar ainda mais seu leque de soluções!


<div role="main" id="blog-s1-dotnet-134e3db1eea6c9829db1"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-dotnet-134e3db1eea6c9829db1', 'UA-48664517-12').createForm();</script>