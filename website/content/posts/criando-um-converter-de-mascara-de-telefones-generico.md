---
title: "Criando Um Converter De Mascara De Telefones Generico"
date: 2015-09-11T19:49:48+02:00
---

Sempre que usamos dados de serviços externos ao nosso app, precisamos nos prevenir quanto a qualidade desses dados. No caso geral, nunca devemos confiar nos dados.

> Nunca confie nos dados!

Um dado que geralmente não damos muita importância é o "número de telefone", no entanto o mesmo número de telefone pode ter diversas variações:

- +55 11 99999 1234
- 55 11 99999 1234
- 11 99999 1234
- 0xx11 99999 1234
- 99999 1234
- +55 11 9999 1234
- 55 11 9999 1234
- 11 9999 1234
- 0xx11 9999 1234
- 9999 1234

Veja que eu ainda considerei alguns números de celular que não contém o prefixo 9 visto que ainda não foi concluída a implantação to prefixo em todo o país.

![Cronograma de implantação do nono dígito para celulares no Brasil. (Fonte: Anatel, 9 de Novembro de 2015)](https://s3-eu-west-1.amazonaws.com/jefersonsantos-blog/2017/11/1412795477426.png)
(Fonte: Anatel, 9 de Novembro de 2015)

Como criar uma máscara **(DDD) + Telefone** que funcione em todos estes casos (ou pelo menos na maioria deles)?

O livro [*The Pragmatic Programmer: From Journeyman to Master*](https://books.google.com.br/books/about/The_Pragmatic_Programmer.html?id=5wBQEp6ruIAC&redir_esc=y) diz que um dos aspectos de um programador pragmático é o questionamento “se este app faz isso, então como ele faz?”. Partindo do mesmo princípio, se o sistema operacional faz essa máscara, como ele faz?

O Google desenvolveu para Android uma biblioteca para fazer o parsing, a formatação, e validação validação de telefones chamada [*libphonenumber*](https://github.com/googlei18n/libphonenumber) que é Open Source e está disponível em java e c++, e foi portada para C# sendo possivel encontrar até no nugget como [*libphonenumber-c#*](https://www.nuget.org/packages/libphonenumber-csharp).

Utilizar esta biblioteca é tão simples quanto:

```csharp
[TestMethod]
public void NumeroComCodPais()
{
    string testPhone = "+5511999991234";
    var result = PhoneNumberUtil.GetInstance().Parse(testPhone, "BR");
    Assert.AreEqual(result.NationalNumber.ToString(), "11999991234");
}
```

Toda a "mágica" acontece no método `PhoneNumberUtil.GetInstance().Parse(string phoneNumber, string defaultCountry)` que retorna uma estrutura onde é possível obter o DDD+Numero em formato ulong.

O *IValueConverter* pode ser implementado da seguinte forma:

```csharp
public object Convert(object value, Type targetType, object parameter, string language)
{
    if (string.IsNullOrEmpty((string)value))
    {
        return string.Empty;
    }

    string template = "({0}) {1}-{2}";
    var result = PhoneNumberUtil.GetInstance().Parse(value.ToString(), "BR");
    string celular = result.NationalNumber.ToString();

    // telefone sem DDD
    if (celular.Length < 10)
    {
        celular = celular.Insert(0, parameter.ToString());
    }

    // Numero sem nono digito
    if (celular.Length == 10)
    {
        return string.Format(template,
                            celular.Substring(0, 2),
                            celular.Substring(2, 4),
                            celular.Substring(6, 4));
    }

    // numero com nono digito
    if (celular.Length == 11)
    {
        return string.Format(template,
                            celular.Substring(0, 2),
                            celular.Substring(2, 5),
                            celular.Substring(7, 4));
    }

    return string.Empty;
}
```

Desta forma sempre teremos o formato "(XX) XXXX-XXXX" ou "(XX) 9XXXX-XXXX". Quando o número não tem DDD podemos utilizar o *object parameter* para receber um valor padrão de DDD.

Podemos utilizar o Converter no XAML com a seguinte notação:

```html
<TextBlock Text="{Binding NumeroTelefone,
                          Converter={StaticResource TelefoneConverter},
                          ConverterParameter='11' }" />
```

Assim, sempre que precisarmos formatar um número de telefone (neste caso celulares) basta adicionar a chamada ao Converter.
