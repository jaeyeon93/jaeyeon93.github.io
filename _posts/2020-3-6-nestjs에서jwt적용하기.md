---
layout: post
title: 2020-3-6 NestJS에서 JWT인증
---


## Authentication

- nest에서 권한패키지로 passport를 많이쓴다. @nestjs/passport
- passport 실행순서
  - user의 credentials을 확인한다.(id,password를 확인 or JWT or identtiy token)
  - authentication 상태를 관리
  - 사용자의 request에 authentication을 붙인다.

```shell
$npm install --save @nestjs/passport passport passport-local
$npm i --save-dev @types/passport-local
```

- passport전략을 선택할때 @nestjs/passport와 passport패키지가 필요하고 그런 다음 구체적인 strategy-specific package(passport-jwt, passport-local)을 설치해야한다.

#### implementing passport strategies

- passport자체를 하나의 미니 프레임워크로 볼 수 있는데, @nestjs/passport 모듈이 nestjs스타일로 wrapping해준다.
- vanilla passport 동작원리
  - set of option들이 passport의 전략을 구체적으로 명시한다. jwt 전략에서는 sign token을 제공한다.
  - 'verify callback'는 어떻게 반응을 하는지 명시. passport라이브러리는 callback에서 validation이 true이면 user를 리턴해준다. false이면 null을 리턴해준다.
  - Passport전략을 PassportStrategy클라스를 확장해서 설정한다. 서브클래스에서 super()메서드를 호출하고 선택적으로 옵션을 전달한다. 또한 validate()메서드를 구현하여 verifiy callback을 제공해야한다.

```typescript
@Injectable()
export class AuthService {
  constructor(private readonly accountsService: AccountsService) {}

  async validateAccount(id: string, email: string, password: string): Promise<any> {
    const account: Account = await this.accountsService.findOne(id);
    console.log(`validateAccount에서 account ${JSON.stringify(account)}`);
    if (account && account.getPassword() == password) {
      const {getPassword, ...result} = account;
      return result;
    }
    return null;
  }
}

```

- 실제 애플리케이션에서는 password대신 salt와 함께 단방향으로 암호화된 bcrpyt를 사용해야한다.
- 그러면 hash화된 password끼리 매칭을 하는것이다.

### built-in passport guard

- guard는 reuqest가 router에서 handle이 되는지 안되는지를 결정한다.
- 앱은 인증관점에서 2가지 상태로 존재할 수 있다.
  - user/clinet is not logged in (not authenticated)
  - user/clinet logged in(is authenticated)

- 로그인이 안된상태에서는 2가지 고유의 함수들이 필요하다.
  - Unauthenticated user가 접근하는것을 막아야한다. Deny access. guard를 통해 routes를 보호할 수 있다. 여기서 JWT가 유효한지를 체크한다.
  - authentication단계를 시작할때, 어떻게 passport-local strategy를 라우팅할지? POST username/password or POST /auth/login이 떠오르지만. @nestjs/passport에서 다른종류의 guard를 사용해야한다.

#### login route

- /auth/login을 구현해야한다.

```typescript
import { Controller, Request, Post, UseGuards } from '@nestjs/common';
import { AuthGuard } from '@nestjs/passport';

@Controller()
export class AppController {
  @UseGuards(AuthGuard('local'))
  @Post('auth/login')
  async login(@Request() req) {
    return req.user;
  }
}

// local.strategy.ts
@Injectable()
export class LocalStrategy extends PassportStrategy(Strategy) {
  constructor(private readonly authService: AuthService) {
    super({
      usernameField: 'email',
      password: 'password',
    });
  }

  // @UseGuard에서('local')을 Strategy로 선택한 controller에 대해서 동작
  // 처음 request가 들어가면 request에 대한 검증을 한다.
  async validate(email: string, password: string): Promise<any> {
    console.log(`local strategy validate`);
    const payload: JwtPayload = {email, password};
    const account = await this.authService.validateAccount(payload);
    if (!account)
      throw new UnauthorizedException('Fail to login try again');
    return account;
  }
}

```

- @UseGuards(AuthGuard('local'))은 passport-local전략을 사용할때, @nestjs/passport에서 규정한 AuthGuard를 사용하겠다는 의미다.
- passport의 기본적인 local전략의 이름은 'local'이다?
- /auth/login라우터는 user를 리턴해준다. 여기서 passport의 특징이 드러나는데, user객체를 자동으로 생성하고 validate()로부터 받은 return값을 돌려주는것이다. 뿐만아니라 Request객체에 req.user로서 할당된다.
- @UseGuard(AuthGuard('local'))이 붙은 라우터에 요청에서는 validate을 우선적으로 통과한다.
