---
title: "Criando Contatos No Windows Phone 8.1"
date: 2015-10-19T20:02:38+02:00
---

Alguns dias atrás estava trabalhando em um projeto cujo aplicativo precisava obter e gravar dados de contato na base de dados do app de contatos do Windows Phone.

Quanto a buscar os dados, é relativamente simples:

```cs
var contatos = ContactManager.RequestStoreAsync();
```

Teoricamente o código abaixo deveria abrir a tela de contatos do Windows Phone com um _ContactCard_ vazio para o usuário preencher.. mas...

```cs
ContactManager.ShowContactCard(new Contact(), new Rect());
```

Infelizmente para adicionar um contato não é tão simples assim no WP8.1. Quando chamamos os métodos que deveriam adicionar contatos ou acionar a tela do próprio WP para adicionar um novo contato somos surpreendidos com a exception: _**NotImplementedException**_.

#### O que fazer então?

Uma alternativa seria fazer todo o gerenciamento de contatos dentro da sua app, e ficar completamente desvinculado da base de dados de contatos do OS. No entanto, toda vez que o usuário adicionasse um contato a partir do seu app, este contato só estaria disponível ali. E isso é péssimo no ponto de vista de usabilidade e integração com OS.

Há porém uma alternativa que pode ser utilizada neste caso. Praticamente todo sistema operacional tem suporte a [vCard](https://en.wikipedia.org/wiki/VCard), logo podemos gerar um vCard e importar no aplicativo People de forma programática.

Primeiramente temos que gerar um vcard para que possa ser interpretado pelo OS:

```csharp
var vcardData = @"BEGIN:VCARD
FN:Mr. John P. Smith, Jr.
TITLE:General Manager
ORG:XYZ Corp.;
North American Division;
Manufacturing ADR;
POSTAL;WORK:;;P.O. Box 10010;AnyCity;AnyState;00000;U.S.A.
LABEL;POSTAL;WORK;ENCODING=QUOTED-PRINTABLE:P.O. Box 10010=0D=0A=Anywhere, TN  37849=0D=0A=U.S.A.
ADR;PARCEL;WORK:;133 Anywhere St.;Suite 360;AnyCity;AnyState;00000;U.S.A.
LABEL;POSTAL;WORK;ENCODING=QUOTED-PRINTABLE:133 Anywhere St.=0D=0A=
Anywhere, TN  37849=0D=0A=U.S.A.
TEL;Work;VOICE;MESG;PREF:+1-234-456-7891 x56473
TEL;Home:+1-234-456-7891
TEL;Pager:+1-234-456-7891
TEL;Cell:+1-234-456-7891
TEL;Modem;FAX:+1-234-456-7891,,*3
EMAIL;Internet:webmaster@anywhere.com
URL:http://www.anywhere.com/mrh.vcf
UID:http://www.anywhere.com/mrh.vcf
TZ:-0500
BDAY:1997-11-29
REV:20090401T065518
VERSION:2.1
END:VCARD";
```

Em seguida, devemos salvar o arquivo temporário **vcf** (extensão dos arquivos vcard) no storage da nossa aplicação:

```cs
StorageFolder folder = ApplicationData.Current.LocalFolder;
StorageFile vcard = await folder.CreateFileAsync("test.vcf", CreationCollisionOption.ReplaceExisting);
await FileIO.WriteTextAsync(vcard, vcardData);
```

E por fim, podemos "jogar a bola" para o sistema operacional com o *LaunchFileAsync*:

```csharp
await Launcher.LaunchFileAsync(vcard);
```

## Importante

Existem alguns “problemas” com esta técnica:

1. O sistema operacional sempre irá perguntar ao usuário se deseja adicionar o vcard ao app de contatos, e com isso temos uma tela intermediária e a necessidade do usuário clicar em salvar em 3 telas diferentes (na tela do seu app, na tela de importação de vcard do OS, e na tela do app de contatos do windows phone).
2. Caso o usuário tenha instalado algum outro app que permita manipular vcard, a tela intermediária de importação apresentará esta opção.
3. Se o usuário não salvar o contato, você não fica sabendo, ou seja, logo que sua app voltar ao foco. Será necessário fazer uma verificação extra para checar se o usuário importou o vcard com sucesso.

A técnica do vcard funciona bem e permite que várias informações do contato já sejam adicionadas programaticamente. Existe a possibilidade de criar um app com target em Windows Phone 8.0, e extender sua funcionalidade por _Uri Schemas_. Desta forma seria possível chamá-lo desde um app com target Windows Phone 8.1. Estou testando esta possibilidade, em breve devo contar a experiência aqui.

👉 [Baixe aqui o código fonte completo do exemplo no Github](https://github.com/dijeferson/POC-VCard).
