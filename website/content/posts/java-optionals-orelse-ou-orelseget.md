---
title: "Java Optionals: OrElse ou OrElseGet"
date: 2018-10-22T14:48:31+02:00
---

No Java 8 foram introduzidos os [Optionals](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html#orElse-T-). Estes são extremamente uteis em vários cenários onde não há certeza de retorno de valores validos.

Uma função que tende a causar um pouco de confusão é a `.orElse` e `.orElseGet`. A princípio parecem iguais mas há diferenças substaciais entre elas no que diz respeito a funcionalidade e performance.

Ao observar a assinatura dos métodos

```java
public T orElse(T other)

public T orElseGet(Supplier<? extends T> other)
```

Podemos ver claramente que `.orElse()` retorna um valor do tipo `T` e aceita um valor do mesmo tipo, enquanto `.orElseGet()` retorna um valor do tipo `T` e aceita uma função como parametro.

Ao checar os javadocs temos:

- `T orElse(T other)`: retorna o valor se estiver presente, caso contrario retorna __other__
- `T orElseGet(Supplier<?> other)`: retorna o valor se estiver, caso contrário invoca __other__ e retorna o valor do seu resultado.

Antes de analisarmos as diferenças, considere o seguinte exemplo:

```c
private static Optional<String> getAnswer(Boolean know) {
    if(!know){
        System.out.println("call: I don't know");
        System.out.println("call: Returning Empty");
        return Optional.empty();
    } else {
        System.out.println("call: I know!");
        System.out.println("call: Returning answer!");
        return Optional.of("call: 42");
    }
}

private static String fallback() {
    System.out.println("fallback: Falling back to a random typed number...");
    return "fallback: 1234567890";
}
```

Vamos as diferenças entre eles:

## orElse()

Considere o seguinte:

```java
System.out.println(getAnswer(false).orElse(fallback()));
```

Agora observemos o resultado no console:

```plaintext
call: I don't know
call: Returning Empty
fallback: Falling back to a random typed number...
fallback: 1234567890
```

Ótimo certo? exatamente o que esperavamos.

Agora, considere que o parametro `know = true`:

```java
System.out.println(getAnswer(true).orElse(fallback()));
```

Observando o console temos:

```plaintext
call: I know!
call: Returning answer!
fallback: Falling back to a random typed number...
call: 42
```

Notou a diferença? o metodo `.orElse` executou tanto o método `getAnswer()` como `fallback()` e por fim utilizou o primeiro resultado valido que encontrou, neste caso, o retorno do `getAnswer()`.

## orElseGet()

Assim como no caso do `orElse`, considere os exeplos abaixo:

```java
System.out.println(getAnswer(false).orElseGet(() -> fallback()));
```

Resultados:

```plaintext
call: I don't know
call: Returning Empty
fallback: Falling back to a random typed number...
fallback: 1234567890
```

Ótimo, exatamente o esperado, assim como no caso do orElse. Agora vejamos o que acontece quando o `getAnswer()` retorna um valor válido:

```java
System.out.println(getAnswer(true).orElseGet(() -> fallback()));
```

Resultados:

```plaintext
call: I know!
call: Returning answer!
call: 42
```

Note que assim que o `getAnswer()` retorna um resultado válido, a execução do próximo metodo (`fallback()`) é automaticamente interrompida, retornando o valor do valido do primeiro metodo.

### Mas e daí?

Imagine que a função `fallback()` modifique arquivos, banco de dados, ou faça chamadas a APIs. Usando `orElse()` você pode acidentalmente criar chamadas desnecessárias, gerar dados incorretos, dentre outros problemas.
Em outras situações pode-se causar confusão ao gerar entradas incorretas em logs, como por exemplo:

```java
priceList = getPrices().orElse(getPricesFromYesterday());
```

Onde cada método (`getPrices()` e `getPricesFromYesterday()` postem logs indicando que estão sendo usados)

O log teria entradas de ambos metodos:

```plaintext
[DEBUG] Using prices updated today.
[DEBUG] Using prices updated yesteday.
```

Ao ver um log assim, qual instância de lista de preços está realmente sendo utilizada? É praticamente impossível dizer sem abrir o código e começar a debugar.

Então, por padrão **evite usar orElse() para métodos, e use orElseGet() sempre que possível, a não ser que haja um motivo claro e concreto.**
