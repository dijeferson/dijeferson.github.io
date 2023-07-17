---
title: "Processamento Paralelo E Assincrono Com Tasks"
date: 2015-11-04T20:00:13+02:00
---

Quando desenvolvemos para dispositivos móveis temos acesso, geralmente, a um **subset** do framework, deixando algumas implementações bastante limitadas.

No caso do *Windows Phone 8.1* não há acesso às bibliotecas de paralelismo disponibilizadas no .NET, então, qual a melhor alternativa? Usar a classe Task.

Dentro da classe Task temos 2 funções que servem bem para o proposito de paralelismo:

- Task.WhenAll
- Task.WhenAny

Como exemplo vamos obter a partir de uma lista de **strings** suas respectivas representações em formato **base64**. No meio do método faremos um **Task.Delay(2500)** de forma que o método demore “pelo menos” 2,5s para concluir sua execução. O método é o seguinte:

```csharp
private static async Task<string> ConvertToBase64Async(string msg)
{
    Console.WriteLine(string.Format("Started ==> {0} {1}",
                                    DateTime.Now,
                                    msg));
    var result = Convert.ToBase64String(Encoding.ASCII.GetBytes(msg));
    await Task.Delay(2500);
    Console.WriteLine(string.Format("Finished ==> {0} {1} >> {2}",
                                    DateTime.Now,
                                    msg,
                                    result));
    return result;
}
```

## Task.WhenAll

Ao utilizar o *WhenAll* só vamos “liberar” a **thread** quando todos as **Tasks** que estão em execução forem finalizadas e retornarem seus respectivos valores.

```csharp
var phrases = new List<string>
{
    "frase 1",
    "frase 2",
    "frase 3",
    "frase 4",
    "frase 5",
    "frase 6",
    "frase 7",
    "frase 8",
    "frase 9"
};

var enc = await Task.WhenAll(phrases.Select(q => ConvertToBase64Async(q)));
Console.WriteLine("**Finished**");
```

Neste exemplo, a linha “**Finished**” só vai ser exibida no console quando a última **Task** concluir sua execução. Antes disso, a **thread** que chamou a função fica em **await**.

## Task.WhenAny

Ao utilizar o *WhenAny* a **thread** que o chamou é liberada, ou seja, sai de estado **awaiting**, assim que a primeira **Task** conclui sua execução.

```csharp
var phrases = new List<string>
{
    "frase 1",
    "frase 2",
    "frase 3",
    "frase 4",
    "frase 5",
    "frase 6",
    "frase 7",
    "frase 8",
    "frase 9"
};

var enc = await Task.WhenAny(phrases.Select(q => ConvertToBase64Async(q)));
Console.WriteLine("**Finished**");
```

Mas e as outras **Tasks**? A execução continua em background, e paralelo a **thread** principal, então assim que forem concluindo, seus resultados são exibidos no console.

## Quando usar?

Existem alguns cenários em que o uso de **Tasks** é bastante interessante como:
 – Download de múltiplos *assets* em paralelo
 – Carregamento progressivo de listas
 – Múltiplas chamadas de serviços (por exemplo: atualização de dados de vários usuários, upload de uma lista de contatos, etc)
