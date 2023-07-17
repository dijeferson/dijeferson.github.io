---
title: "Implementando Hmac Sha1 No Winrt"
date: 2015-12-17T14:57:18+02:00
---


Em um projeto que trabalhei havia uma parte da implementação que requeria que fosse gerada uma hash *HMAC-SHA-1*. No .NET é relativamente simples fazer isso, basta fazer a chamada:

```csharp
var hmac = new HMACSHA1(key).ComputeHash(data);
```

E simples assim é gerada a hash *HMAC-SHA-1*. No entanto, no WinRT a coisa muda um pouco pois nem mesmo a classe *HMACSHA1* está disponível no *System.Security.Cryptography*!

A mesma implementação no WinRT fica da seguinte forma:

```csharp
private static string HmacSha1(string key, string message)
{
    // instanciando o algoritmo de criptografia
    var crypt = MacAlgorithmProvider.OpenAlgorithm(MacAlgorithmNames.HmacSha1);

    // convetendo os dados para binario (tanto chave como msg a ser criptografada)
    var messageBuffer = CryptographicBuffer.ConvertStringToBinary(message, BinaryStringEncoding.Utf8);
    var keyBuffer = CryptographicBuffer.ConvertStringToBinary(key, BinaryStringEncoding.Utf8);

    // gerando chave de criptografia
    var cryptKey = crypt.CreateKey(keyBuffer);

    // gerando hash de assinatura da mensagem
    var signature = CryptographicEngine.Sign(cryptKey, messageBuffer);

    // retornando a hash em hexadecimal.
    return CryptographicBuffer.EncodeToHexString(signature);
}
```

Podemos usar algumas strings clássicas como teste neste método:
 `key = “key”`
 `message = “The quick brown fox jumps over the lazy dog”`
 `resultado = de7c9b85b8b78aa6bc8a7a36f70a90701c9db4d9`

Para aprender mais sobre o HMAC-SHA1 clique [aqui](https://pt.wikipedia.org/wiki/HMAC).
