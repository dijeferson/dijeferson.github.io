---
title: "Enviando Emails Em .NET"
date: 2017-10-31T14:55:59+02:00
---

Enviar emails diretamente de aplicativos tem diversas vantagens e utilizações.

Em algumas linguagens de programação a criação de rotinas para envio de mensagens utilizando servidores SMTP pode ser tão complexa que se torna pouco viável sem a utilização de componentes adicionais (que na maioria das vezes são pagos).

No .NET 2.0 já existe um namespace próprio para o tratamento de envio de mensagens por email. É o `System.Net.Mail`.

Para enviar um email, não é preciso mais do que 10 linhas!

```csharp
using System.Net.Mail;

    // ...

    public void sendMail(string from, string to, string subject, string message)
    {
        MailMessage message = new MailMessage();
        message.From = new MailAddress(from);

        // Pode-se perceber que é possivel adicionar
        // quantos destinatarios forem necessarios com message.To.Add()
        message.To.Add(new MailAddress(to));
        message.Subject = subject;
        message.Body = message;

        SmtpClient client = new SmtpClient();

        client.Host = "localhost";
        client.Port = 25;
        client.Send(message);
}
```

Como é possivel verificar, é bem simples! Algumas informações como servidor e porta podem ser exportadas para um arquivo de configuração para tornar a função ainda mais flexível.

Essa pequena função acima pode ser inserida dentro de algum tipo de trigger, para que dependendo do evento, o email seja enviado.
