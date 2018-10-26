---
title: "[Amazon] Amazon SES를 통한 메일발송"
layout: post
comments: true
excerpt: "smtp를 이용하여 이메일 발송을 해본다."
tags:
  - Amazon
date: 2018-10-27

---

# Amazon SES + sendmail

Amazon SES에서 이메일 전송 방법에는 3가지 방법이 있습니다.

- 콘솔을 통한 이메일 발송
  - 최소 설정으로 테스트 메일을 발송해보려면 이 방법을 사용하는 것이 가장 효율적입니다.
    테스트에 성공하여 프로덕션 이메일 전송을 시작할 준비가 되면 다른 2개의 방법 중 하나를 사용하도록 합니다.
- SMTP를 사용
  - Amazon SES SMTP 인터페이스를 통해 이메일을 전송할 수 있습니다.
- AWS SDK를 사용
  - AWS SDK API를 호출하여 이메일을 전송합니다.

<br>

## Console을 통한 이메일 발송

Amazon SES에서 할 수 있는 이메일 발송 방법 중 가장 쉬운 방법이며, 사용자가 수동으로 입력해야하므로 **테스트 용으로만 사용하도록 합니다.**

1. AWS Management 콘솔에 로그인 한 후 https://console.aws.amazon.com/ses/ 에서 Amazon SES 콘솔을 엽니다.

2. [**Identity Management**] 아래에서 [**Email Addresses**] 선택한 후 [**Verify a New Email Address**]버튼을 통해 이메일을 등록합니다.
3. 메일함에 온 인증메일을 통해 확인절차를 거칩니다.
4. [**Send a Test Email**] 을 선택하여 [**Email Format**]을 선택하고 formatted 옵션에 등록된 이메일 주소로 테스트 메일을 전송해봅니다.

<br>


## Amazon SES SMTP를 이용하여 이메일 전송

### Amazon SES SMTP 자격 증명받기

SMTP인터페이스에 접근하기 위해서는 사용자 이름과 암호가 필요합니다. 모든 리전에서 동일한 smtp자격 증명 세트를 사용할 수 있습니다.

>  AWS 자격증명과, smtp 자격증명은 서로 다른 개념입니다.
>  AWS자격증명이 있다고 하여, smtp 인터페이스에 액세스 할 수 있는 것은 아닙니다.

smtp 자격증명을 받는 방법은 2가지로 나뉩니다.

- Amazon SES console을 통해 자격증명
- AWS 자격증명에 SMTP 자격증명을 추가

console을 이용한 자격증명만을 설명합니다.

1. AWS Management console에 로그인하여 SES console로 이동합니다.
2. region을 선택한 후 탐색창에서 [**SMTP Settings**]를 선택합니다.
3. 콘텐츠 창에서 [**Create My SMTP Creditionals**]를 선택합니다.
4. **Create User for SMTP**를 클릭하여 사용자를 생성합니다.
5. [**Show User SMTP Credentials**]를 선택하여 자격증명 파일을 다운로드 받습니다.

<br>

## 확인되지 않은 이메일 주소로 메일을 보내는 방법

위의 절차를 통해 smtp인터페이스에 접근하여 메일 발송을 할 경우 SandBox 환경에 속해 있는 계정만이 이메일을 송/수신 할 수 있습니다.

SMTP 자격증명이 있고, SandBox에 배치된 계정들은 Amazon SES의 거의 모든 기능을 사용할 수 있지만 4가지 제약을 갖습니다.

1. 확인된 메일주소, 도메인**으로만** 메일을 보낼 수 있습니다.
2. 확인된 메일주소, 도메인**에서만** 메일을 보낼 수 있습니다.
3. 24시간 동안 최대 200개의 메시지를 보낼 수 있습니다.
4. 초당 최대 1개의 메시지를 보낼 수 있습니다.

위와 같은 제약사항으로 인해 SandBox에서 삭제해 줄 것을 요청해야합니다.

<br>

## Amazon SES SandBox환경에서 나가기

확인되지 않은 이메일 주소로 메일을 보내고, 하루에 보낼 수 있는 메일의 수와 발송 속도를 높히기 위해서는 SandBox환경에서 계정을 전환해야합니다.

1. AWS Managemet console에 로그인 한 후 SES Console로 이동합니다.
2. 지원 센터에서 SES Sending Limits Increase case를 엽니다.
3. 양식에 노출되는 섹션 중 몇 가지를 설명합니다.
   1. region : 이메일을 보내기 위해 사용할 AWS Region입니다.
   2. Limit : **Desired Daily Sending Quota**(일일 발신 할당량), **Desired Maximum Send Rate**(필요한 최대 전송 속도)를 선택합니다.
   3. New limit value : 생신하고 싶은 발신 할당량, 전송 속도를 입력합니다.(기본값은 최대 200개의 메시지, 초당 1개의 메시지입니다.)

4. [**Submit**]을 클릭합니다. 
5. 요청을 검토하는데 1일이 소요되며 SES Sending Limits Increase 요청에 대한 통신문을 받으면 요청이 승인되었음을 나타냅니다.
