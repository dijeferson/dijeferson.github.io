---
title: "Parser De Json usando Dynamic No WinRT"
date: 2015-11-05T19:56:25+02:00
---

Eu estava trabalhando em uma funcionalidade de um app em que era necessário desserializar um json e utilizar seus dados para processamento. Como era uma estrutura pequena e sem grande impacto optei por fazer uma desserialização para um **dynamic type** que “em teoria” é tão simples quanto:

```csharp
string content = @"{""FullName"" : ""Nikola Tesla"", ""Phone"" : ""5511999999999"" , ""Email"" : ""ntesla@test.com"" }";
dynamic userInfo = JObject.Parse(content);

var _name = userInfo.FullName;
var _phone = userInfo.Phone;
var _email = userInfo.Email;
```

O _JObject_ faz o Parse do JSON para uma estrutura anônima e dinâmica chamada userInfo e podemos utilizar os campos do JSON como propriedades da estrutura userInfo. Simples, limpo, elegante, e direto.

Mas.. no *Windows Phone* nem tudo é tão fácil assim. Aparentemente esta técnica não funciona no .NET do Windows Phone, retornando uma *exception*:

> *Newtonsoft.Json.Linq.JObject’ does not contain a definition for ‘FullName’*

Após alguma investigação cheguei a dois cenários de workaround para o problema:

## Workaround #1: Fazer o *parse* para um *anonymous* type manualmente.

É desagradável, fere os conceitos de DRY e dependendo do tamanho do seu json vai dar um certo trabalho.

```csharp
string content = @"{""FullName"" : ""Nikola Tesla"", ""Phone"" : ""5511999999999"" , ""Email"" : ""ntesla@test.com"" }";

var json = JObject.Parse(content);

var userInfo = new
{
    FullName = json["FullName"].ToString(),
    Phone = json["Phone"].ToString(),
    Email = json["Email"].ToString()
};

var _name = userInfo.FullName;
var _phone = userInfo.Phone;
var _email = userInfo.Email;
```

## Workaround #2: Transformar o JObject em um ExpandoObject

Pode-se utilizar o ExpandoObject receber os dados do JObject em forma de IDictionary e assim converter automaticamente para uma estrutura dinâmica com propriedades.

```csharp
string content = @"{""FullName"" : ""Nikola Tesla"", ""Phone"" : ""5511999999999"" , ""Email"" : ""ntesla@test.com"" }";

var json = JObject.Parse(content);
dynamic exp = new ExpandoObject();

foreach (var item in json)
{
    (exp as IDictionary<string, object>).Add(item.Key, item.Value);
}

var _name = userInfo.FullName;
var _phone = userInfo.Phone;
var _email = userInfo.Email;
```

Neste caso considerei que não há estruturas internas no json, caso houvesse seria necessário um método recursivo. Optei pelo “workaround 2” por ser menor e mais simples.
