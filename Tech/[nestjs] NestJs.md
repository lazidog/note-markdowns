# NestJs

## Overview

### @Controller

- Handle incoming request

  > To create controller with `validation` built-in controller use command '`nest g resource [name]`' - or not, use '`nest g controller [name]`'

**`Reponse`**:

- Standard (built-in): object returned from request handler will automatically be serialized to JSON
- Library-specific: use library (e.g., express) response object (e.g., `findAll(@Res() response)`)

  > **Note**: If both approaches are used at the same time, the Standard approach is `automatically disabled`. Solution `@Res({ passthrough: true })`

<ins>Example:</ins>

```ts
import { Controller, Get, Req } from '@nestjs/common';
import { Request, Response } from 'express';

@Controller('cats')
export class CatsController {
  @Get()
  findAll(@Req() request: Request, @Res() response: Response): string {
    return 'This action returns all cats';
  }
}
```

[`Custom Decoration`](https://docs.nestjs.com/custom-decorators)

**`Asynchronously`**

> Nest route handlers are even more powerful by being able to return `RxJS observable streams`. Nest will automatically subscribe to the source underneath and take the last emitted value (once the stream is completed).

[The introduction to Reactive Programming you've been missing](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)

```js
@Get()
findAll(): Observable<any[]> {
  return of([]);
}
```

**`DTO (Data Transfer Object)`**

- A DTO is an object that defines how the data will be sent over the network.

  [Interfaces vs Classes](https://stackoverflow.com/questions/40973074/difference-between-interfaces-and-classes-in-typescript/55505227#55505227)

```js
export class CreateCatDto {
  name: string;
  age: number;
  breed: string;
}
```

### Providers

**`Dependency injection`**

> `Dependency injection` is an `inversion of control (IoC)` technique: delegate dependency instantiation to the IoC container (NestJS runtime system), instead of doing it manually.
>
> The `@Injectable()` decorator marks a class as a provider.

**`Injection patern`**

```js
// Standard
@Injectable()
export class AService {}
@Controller('controllername')
export class AController  {
  constructor(private aService: AService)
}
@Module({
  controllers: [AController],
  providers: [AService],
})
export class AppModule {}

// non-class-based provider
const configFactory = { // separate and export provider with a full object
  provide: 'CONFIG',
  useFactory: () => {
    return process.env.NODE_ENV === 'development' ? devConfig : prodConfig;
  },
}
@Module({
  providers: [
    {
      provide: 'CONNECTION',
      useValue: connection,
    },
    configFactory
  ],
  exports: ['CONNECTION', configFactory]
})
export class AppModule {}
@Injectable()
export class CatsRepository {
  constructor(@Inject('CONNECTION') connection: Connection) {}
}
```

**`Scopes`**

- **DEFAULT**: A singleton instance is shared across the entire application
- **REQUEST**: new instance for each incoming request
- **TRANSIENT**: Not shared across consumers, each consumer will receive a new, dedicated instance.

```js
import { Injectable, Scope } from '@nestjs/common';

@Module({
  providers: [
    {
      provide: 'CACHING',
      useValue: caching,
      scope: Scope.TRANSIENT, // transient scope
    },
  ],
  exports: ['CACHING'],
})
export class AppModule {}

// request scope
@Injectable({ scope: Scope.REQUEST })
export class CatsService {}
```

If you want to access the original request object when using request-scoped provider, just inject `REQUEST` in `@nestjs/core` or `CONTEXT` in `nestjs/graphql`

```js
import { REQUEST } from '@nestjs/core';
import { CONTEXT } from '@nestjs/graphql';
import { Request } from 'express';

@Injectable({ scope: Scope.REQUEST })
export class CatsService {
  constructor(@Inject(REQUEST) private request: Request) {}
  // or graphql
  constructor(@Inject(CONTEXT) private context) {}
}
```

**`Inquirer provider`**

To get class where a provider was constructed (for logging,...), just inject `INQUIRER` token

```js
import { INQUIRER } from '@nestjs/core';
export class LoggerService {
  constructor(@Inject(INQUIRER) private parentClass: object) {}

  log(message: string) {
    console.log(`${this.parentClass?.constructor?.name}: ${message}`);
  }
}
@Injectable()
export class AppService {
  constructor(private loggerService: LoggerService) {}

  doSomething(): string {
    this.loggerService.log('My name is getRoot');
  }
}
```
