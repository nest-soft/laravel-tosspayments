# A Laravel package to Toss Payments


[![Latest Version on Packagist](https://img.shields.io/packagist/v/getsolaris/laravel-tosspayments.svg?style=flat-square)](https://packagist.org/packages/getsolaris/laravel-tosspayments)
[![GitHub Tests Action Status](https://img.shields.io/github/workflow/status/getsolaris/laravel-tosspayments/run-tests?label=tests)](https://github.com/getsolaris/laravel-tosspayments/actions?query=workflow%3Arun-tests+branch%3Amain)
[![GitHub Code Style Action Status](https://img.shields.io/github/workflow/status/getsolaris/laravel-tosspayments/Fix%20PHP%20code%20style%20issues?label=code%20style)](https://github.com/getsolaris/laravel-tosspayments/actions?query=workflow%3A"Fix+PHP+code+style+issues"+branch%3Amain)
[![Total Downloads](https://img.shields.io/packagist/dt/getsolaris/laravel-tosspayments.svg?style=flat-square)](https://packagist.org/packages/getsolaris/laravel-tosspayments)


토스페이먼츠 (Toss Payments) 라라벨 API 입니다. 

### API 버전: 2022-07-27
[API 버전 정책](https://docs.tosspayments.com/reference/versioning#%EB%82%B4-%EC%83%81%EC%A0%90%EC%9D%98-api-%EB%B2%84%EC%A0%84-%ED%99%95%EC%9D%B8%EB%B3%80%EA%B2%BD%ED%95%98%EA%B8%B0)

---

# 설치
```bash
composer require getsolaris/laravel-tosspayments
```

`.env` 에 아래의 환경변수가 추가되어야 합니다.
Toss Payments 개발자센터에서 발급받은 클라이언트 키와 시크릿 키를 환경변수에 추가합니다.

```bash
TOSS_PAYMENTS_CLIENT_KEY=
TOSS_PAYMENTS_SECRET_KEY=
```

`config` 파일을 생성하기 위해서 아래 명령어를 수행합니다.

```bash
php artisan vendor:publish --provider="Getsolaris\LaravelTossPayments\TossPaymentsServiceProvider" --tag="config"
```

# 사용

Toss Payments 개발자센터의 [코어 API](https://docs.tosspayments.com/reference) 를 참고합니다.

API 를 사용하기 앞서 인증을 위한 API 키 준비와 인증 관련된 문서는 [해당 페이지](https://docs.tosspayments.com/guides/using-api)에서 확인 가능합니다.

Basic 인증 방식은 `{SECRET_KEY}:` 를 Base64 인코딩 한 값을 사용합니다.



## [결제 (Payment)](https://docs.tosspayments.com/reference#%EA%B2%B0%EC%A0%9C)

### [결제승인](https://docs.tosspayments.com/reference#%EA%B2%B0%EC%A0%9C-%EC%8A%B9%EC%9D%B8)

POST /v1/payments/confirm

```php
use Getsolaris\LaravelTossPayments\TossPayments;
use Getsolaris\LaravelTossPayments\Attributes\Payment;

$payment = TossPayments::for(Payment::class)
    ->paymentKey($paymentKey)
    ->orderId($orderId)
    ->amount($amount)
    ->confirm();

return $payment->json();
```

### [paymentKey로 결제 조회](https://docs.tosspayments.com/reference#paymentkey%EB%A1%9C-%EA%B2%B0%EC%A0%9C-%EC%A1%B0%ED%9A%8C)

GET /v1/payments/{paymentKey}

```php
use Getsolaris\LaravelTossPayments\TossPayments;
use Getsolaris\LaravelTossPayments\Attributes\Payment;

$payment = TossPayments::for(Payment::class)
    ->paymentKey($paymentKey)
    ->get();

return $payment->json();
```

### [orderId로 결제 조회](https://docs.tosspayments.com/reference#orderid%EB%A1%9C-%EA%B2%B0%EC%A0%9C-%EC%A1%B0%ED%9A%8C)

GET /v1/payments/orders/{orderId}

```php
use Getsolaris\LaravelTossPayments\TossPayments;
use Getsolaris\LaravelTossPayments\Attributes\Payment;

$payment = TossPayments::for(Payment::class)
    ->orderId($orderId)
    ->get();

return $payment->json();
```

### [결제 취소](https://docs.tosspayments.com/reference#%EA%B2%B0%EC%A0%9C-%EC%B7%A8%EC%86%8C)

POST /v1/payments/{paymentKey}/cancel

```php
use Getsolaris\LaravelTossPayments\TossPayments;
use Getsolaris\LaravelTossPayments\Attributes\Payment;

$payment = TossPayments::for(Payment::class)
    ->paymentKey($paymentKey)
    ->cancelReason('고객 변심')
    ->cancel(
        refundReceiveAccount: new RefundReceiveAccount(
            bank: '11',
            accountNumber: '111111111111',
            holderName: '홍길동'
        )
    );

return $payment->json();
```

### [카드 번호 결제](https://docs.tosspayments.com/reference#%EC%B9%B4%EB%93%9C-%EB%B2%88%ED%98%B8-%EA%B2%B0%EC%A0%9C)

POST /v1/payments/key-in

```php
use Getsolaris\LaravelTossPayments\TossPayments;
use Getsolaris\LaravelTossPayments\Attributes\Payment;

$keyIn = TossPayments::for(Payment::class)
    ->amount($amount)
    ->orderId($orderId)
    ->orderName($orderName)
    ->cardNumber($cardNumber)
    ->cardExpirationYear($cardExpirationYear)
    ->cardExpirationMonth($cardExpirationMonth)
    ->customerIdentityNumber($customerIdentityNumber)
    ->keyIn();

return $keyIn->json();
```


### [가상계좌 발급 요청](https://docs.tosspayments.com/reference#%EA%B0%80%EC%83%81%EA%B3%84%EC%A2%8C-%EB%B0%9C%EA%B8%89-%EC%9A%94%EC%B2%AD)

POST /v1/virtual-accounts

```php
use Getsolaris\LaravelTossPayments\TossPayments;
use Getsolaris\LaravelTossPayments\Attributes\Payment;

$virtualAccounts = TossPayments::for(Payment::class)
    ->amount($amount)
    ->orderId($orderId)
    ->orderName($orderName)
    ->customerName($customerName)
    ->bank('우리')
    ->virtualAccounts();

return $virtualAccounts->json();
```

## [거래 (Transaction)](https://docs.tosspayments.com/reference#%EA%B1%B0%EB%9E%98)

### [거래 조회](https://docs.tosspayments.com/reference#%EA%B1%B0%EB%9E%98-%EC%A1%B0%ED%9A%8C)

GET /v1/transactions

```php
use Getsolaris\LaravelTossPayments\TossPayments;
use Getsolaris\LaravelTossPayments\Attributes\Transaction;

$transactions = TossPayments::for(Transaction::class)
    ->startDate('2022-01-01')
    ->endDate('2022-12-31')
    ->get();

return $transactions->json();
```

## [자동 결제 (Billing)](https://docs.tosspayments.com/reference#%EC%9E%90%EB%8F%99-%EA%B2%B0%EC%A0%9C)

### [customerKey로 카드 자동 결제 빌링키 발급 요청](https://docs.tosspayments.com/reference#customerkey%EB%A1%9C-%EC%B9%B4%EB%93%9C-%EC%9E%90%EB%8F%99-%EA%B2%B0%EC%A0%9C-%EB%B9%8C%EB%A7%81%ED%82%A4-%EB%B0%9C%EA%B8%89-%EC%9A%94%EC%B2%AD)

POST /v1/billing/authorizations/card

```php
use Getsolaris\LaravelTossPayments\TossPayments;
use Getsolaris\LaravelTossPayments\Attributes\Billing;

$billing = TossPayments::for(Billing::class)
    ->customerKey($customerKey)
    ->cardNumber($cardNumber)
    ->cardExpirationYear($cardExpirationYear)
    ->cardExpirationMonth($cardExpirationMonth)
    ->customerIdentityNumber($customerIdentityNumber)
    ->authorizationsCard();

return $billing->json();
```

### [authKey로 카드 자동 결제 빌링키 발급 요청](https://docs.tosspayments.com/reference#authkey%EB%A1%9C-%EC%B9%B4%EB%93%9C-%EC%9E%90%EB%8F%99-%EA%B2%B0%EC%A0%9C-%EB%B9%8C%EB%A7%81%ED%82%A4-%EB%B0%9C%EA%B8%89-%EC%9A%94%EC%B2%AD)

POST /v1/billing/authorizations/issue

```php
use Getsolaris\LaravelTossPayments\TossPayments;
use Getsolaris\LaravelTossPayments\Attributes\Billing;

$billing = TossPayments::for(Billing::class)
    ->customerKey($customerKey)
    ->authKey($authKey)
    ->authorizationsIssue();

return $billing->json();
```

### [카드 자동 결제 승인 요청](https://docs.tosspayments.com/reference#%EC%B9%B4%EB%93%9C-%EC%9E%90%EB%8F%99-%EA%B2%B0%EC%A0%9C-%EC%8A%B9%EC%9D%B8-%EC%9A%94%EC%B2%AD)

POST /v1/billing/{billingKey}

```php
use Getsolaris\LaravelTossPayments\TossPayments;
use Getsolaris\LaravelTossPayments\Attributes\Billing;

$billing = TossPayments::for(Billing::class)
    ->customerKey($customerKey)
    ->authKey($authKey)
    ->authorizationsIssue();

return $billing->json();
```

## [정산 (Settlement)](https://docs.tosspayments.com/reference#%EC%A0%95%EC%82%B0)

### [정산 조회](https://docs.tosspayments.com/reference#%EC%A0%95%EC%82%B0-%EC%A1%B0%ED%9A%8C)

GET /v1/settlements

```php
use Getsolaris\LaravelTossPayments\TossPayments;
use Getsolaris\LaravelTossPayments\Attributes\Settlement;

$settlements = TossPayments::for(Settlement::class)
    ->startDate($startDate)
    ->endDate($endDate)
    ->get();

return $settlements->json();
```

### [수동 정산 요청](https://docs.tosspayments.com/reference#%EC%88%98%EB%8F%99-%EC%A0%95%EC%82%B0-%EC%9A%94%EC%B2%AD)

POST /v1/settlements

```php
use Getsolaris\LaravelTossPayments\TossPayments;
use Getsolaris\LaravelTossPayments\Attributes\Settlement;

$settlement = TossPayments::for(Settlement::class)
    ->paymentKey($paymentKey)
    ->request();

return $settlement->json();
```

## [테스트 코드 사용하기](https://docs.tosspayments.com/reference/error-codes#%EC%97%90%EB%9F%AC-%EC%BD%94%EB%93%9C)

[에러코드](https://docs.tosspayments.com/reference/error-codes#%EC%97%90%EB%9F%AC-%EC%BD%94%EB%93%9C)를 확인하여
특정 에러가 발생했을 때와 같이 예상된 시나리오를 직접 발생시켜 처리해 볼 수 있습니다.

```php
use Getsolaris\LaravelTossPayments\TossPayments;
use Getsolaris\LaravelTossPayments\Attributes\Transaction;

$transactions = TossPayments::for(Transaction::class)
    ->startDate('2022-01-01T00:00:00')
    ->endDate('2022-12-31T00:00:00')
    ->testCode('INVALID_CARD_EXPIRATION')
    ->get();
```


## Resource
- [Toss Payments 개발자센터](https://developers.tosspayments.com/)
- [Toss Payments 코어 API](https://docs.tosspayments.com/reference)

## Changelog

Please see [CHANGELOG](CHANGELOG.md) for more information on what has changed recently.

## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.