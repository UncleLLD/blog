---
layout: '../../layouts/MarkdownPost.astro'
title: '实战教程：如何在API监控中实现高效报警和通知'
pubDate: 2023-09-25
description: '本文介绍如何通过gmail监控api接口异常情况'
author: 'UncleLLD'
cover:
    url: 'https://i.imgs.ovh/2023/09/27/PUdCu.jpeg'
    alt: 'cover'
tags: ["后端"]
theme: 'dark'
featured: true

---



## 问题

因一业务需要，想要对API服务接口添加一些监控，以帮助跟踪应用程序的性能、问题和用户活动等。实现监控的方式有多种多样的方式，以下是一些常用的方法：

1. **日志记录**：
   - 在应用程序中添加详细的日志记录，包括请求日志、错误日志以及关键操作的日志。可以使用 Python 的内置 `logging` 模块来实现日志记录。
   - 将日志记录集中到一个位置，例如 Elasticsearch、Logstash 和 Kibana（ELK Stack）或者用于日志聚合的云服务。

2. **性能监控**：
   - 使用性能监控工具来跟踪 API 的响应时间、吞吐量和资源使用情况。一些常见的性能监控工具包括 New Relic、Datadog 和 AppDynamics。
   - 使用 Python 的性能分析工具，如 cProfile 或 Pyflame，来分析性能瓶颈。

3. **错误监控**：
   - 使用错误监控工具来捕获应用程序中的异常和错误，以及它们的频率和影响。一些常见的错误监控工具包括 Sentry、Rollbar 和 Bugsnag。

4. **用户分析**：
   - 使用用户分析工具来了解用户在应用程序中的行为和需求。Google Analytics 和 Mixpanel 是一些流行的用户分析工具。

5. **安全监控**：
   - 使用安全监控工具来检测和防止潜在的安全威胁和攻击。例如，可以使用 WAF（Web 应用程序防火墙）来保护 API。

6. **自定义指标**：
   - 根据应用程序的特定需求，添加自定义监控指标。这些指标可以帮助跟踪应用程序的关键性能参数。

7. **报警和通知**：
   - 设置警报和通知机制，以便在应用程序出现重大问题或异常情况时及时通知团队组员。这可以通过电子邮件、短信或集成到团队通信工具中来实现。

8. **可视化仪表板**：
   - 创建一个仪表板，用于显示监控数据和指标。可以使用工具如 Grafana 或自定义的仪表板来呈现数据。

9. **持续集成/持续部署 (CI/CD)**：
   - 在 CI/CD 流程中集成监控测试，确保在部署新版本时，不会引入性能问题或错误。

10. **定期审查和改进**：
   - 定期审查监控数据，识别问题并采取措施来改进应用程序的性能和可靠性。

上述请根据应用程序需求和预算，选择适合的监控工具和解决方案。此外，还要确保保护用户隐私和敏感数据，并合规监控数据的收集和存储。这里选择**报警和通知**的方式去实现API接口监控问题。

## 实现

为了实现报警和通知机制，可以考虑以下几种方法：

1. **电子邮件通知**：
   - 可以使用 Python 中的邮件库（如 smtplib）来编写脚本，以便在出现重大问题时发送电子邮件通知给团队成员。这需要配置一个发送电子邮件的邮箱帐户。

2. **短信通知**：
   - 如果希望通过短信发送通知，可以使用短信通知服务提供商的 API，如 Twilio 或 Nexmo。这些服务允许通过 API 发送短信通知。

3. **团队通信工具集成**：
   - 将报警和通知集成到团队通信工具（如 Slack、Microsoft Teams 或 Discord）中，以便团队成员能够实时接收通知。这通常涉及使用通信工具的 API 或 Webhook。

4. **监控工具自带的通知功能**：
   - 如果使用性能监控工具或错误监控工具，它们通常具有内置的通知功能，可以根据配置向您发送警报。

5. **云服务提供商的监控和通知服务**：
   - 如果的应用程序托管在云平台上，例如 AWS、Azure 或 Google Cloud，这些云平台通常提供了监控和通知服务，可与应用程序集成。

下面是一个示例，演示如何使用 Python 的 smtplib 库来发送电子邮件通知的基本代码，这里使用的是Gmail：

```python
import smtplib
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart

def send_email(subject, message, to_email):
    # 配置发件人邮箱信息
    from_email = "your_email@gmail.com"
    password = "your_password"
    smtp_server = "smtp.gmail.com"
    smtp_port = 587

    # 创建邮件
    msg = MIMEMultipart()
    msg["From"] = from_email
    msg["To"] = to_email
    msg["Subject"] = subject

    # 添加邮件正文
    msg.attach(MIMEText(message, "plain"))

    # 连接到 SMTP 服务器并发送邮件
    server = smtplib.SMTP(smtp_server, smtp_port)
    server.starttls()
    server.login(from_email, password)
    server.sendmail(from_email, to_email, msg.as_string())
    server.quit()

# 使用示例
subject = "重大问题通知"
message = "应用程序出现重大问题，请立即处理。"
to_email = "team@example.com"
send_email(subject, message, to_email)
```

请注意，上述代码中的邮箱和密码是示例，请替换为自己的发件人邮箱和密码。此外，建议将敏感信息（如密码）存储在环境变量中，以增加安全性。

上述代码使用Gmail作为发送方的时候遇到的问题如下：

```
smtplib.SMTPAuthenticationError: (535, b'5.7.8 Username and Password not accepte
d. Learn more at\n5.7.8 http://support.google.com/mail/bin/answer.py?answer=1425
7\n5.7.8 {BADCREDENTIALS} s10sm9426107qam.7 - gsmtp')
```

查询相关资料是**谷歌已经取消了对其的支持 不建议使用应用程序密码，并且在大多数情况下没有必要。为了确保帐户安全，请使用“使用 Google 登录”将应用程序连接到 Google 帐户。** 

可以使用"app passwords"解决上述用户名密码问题，用户名不变，改用app密码即可。生成"app passwords"的步骤也很简单：

* 第一步：开启Gmail两步验证： **[2-step Verification(google)](https://myaccount.google.com/u/1/security)**

![|inline](https://lets-code-more.s3.amazonaws.com/static/uploads/2022/09/28/app_password.PNG)

* 第二步：创建一个APP，并生成 APP password(要记住的，不行就截一张图) ： **[Generate App-password](https://myaccount.google.com/u/1/apppasswords)**

![|inline](https://lets-code-more.s3.amazonaws.com/static/uploads/2022/09/28/password.PNG)

* 第三步：用生成的密码替换上述代码中的` your_password`即可

> 注意：
>
> * 其他的email是否会有相同的问题不太确定
> * 如果要使用gmail，确保服务器能ping 通gmail.com

## 结果

![|inline](https://i.imgs.ovh/2023/09/27/PU5zT.png)

邮箱能够正常收到Gmail的提示，后续将函数嵌入到API服务中即可完成异常的时候通过邮件告警的目的。

## 参考

* [https://stackoverflow.com/questions/16512592/login-credentials-not-working-with-gmail-smtp/77120467#77120467](https://stackoverflow.com/questions/16512592/login-credentials-not-working-with-gmail-smtp/77120467#77120467)
* [https://www.letscodemore.com/blog/smtplib-smtpauthenticationerror-username-and-password-not-accepted/](https://www.letscodemore.com/blog/smtplib-smtpauthenticationerror-username-and-password-not-accepted/)

