# NestJs

## Overview

### Controller

```bash
nest g resource [name] # create a CRUD controller
nest g controller [name] # create only class controller
```

**`Reponse`**:

- Standard (built-in): object returned from request handler will automatically be serialized to JSON
- Library-specific: use library (e.g., express) response object (e.g., `findAll(@Res() response)`)

  > **Note**: If both approaches are used at the same time, the Standard approach is `automatically disabled`. Solution `@Res({ passthrough: true })`

**`Asynchronously`**

- Nest route handlers are even more powerful by being able to return `RxJS observable streams`. Nest will automatically subscribe to the source underneath and take the last emitted value (once the stream is completed).

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

---

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

  ```ts
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

---

### Modules

- Share modules

  By default, module is a **share module**, any module that `import` a module can have access to the services it share via `export`.

  ```js
  @Module({
    imports: [CommonModule],
    exports: [CommonModule], // re-export
  })
  export class CoreModule {}
  ```

- Scope

  Unlike `Angular` - providers are **global scope**, once defined, available everywhere. `Nest` encapsulates providers inside **module scope**, can't use without import.

  Use decoration `@Global()` to make module available everywhere.

- Dynamic modules

  [example](https://github.com/nestjs/nest/tree/master/sample/25-dynamic-modules)

  Methods like `register`, `registerAsync`, `forRoot`, `forRootAsync`, `forFeature`, `forFeatureAsync` are not a name convention or a rule, u can name it what u want. But nest recommend to follow those [community guidelines](https://docs.nestjs.com/fundamentals/dynamic-modules#community-guidelines)

  The example above can be rewrite with class `ConfigurableModuleBuilder` to auto-generate module like [this](https://github.com/lazidog/practices/tree/main/nestjs-practice/src/config)

## Fundementals

### Excution context

**`ArgumentsHost`**

The `ArgumentsHost` class provides methods for retrieving the `arguments` from appropriate context **(e.g., Http, RPC, WebSocket)**. For example, for REST applications, the host object encapsulates Express's `[request, response, next]` array. On the other hand, for GraphQL applications, the host object contains the `[root, args, context, info]` array

```ts
if (host.getType() === 'http') {
  const [req, res, next] = host.getArgs();
} else if (host.getType() === 'rpc') {
} else if (host.getType<GqlContextType>() === 'graphql') {
}
```

**`ExecutionContext`**

`ExecutionContext` extends `ArgumentsHost`, providing additional details about the current execution process.

```ts
export interface ExecutionContext extends ArgumentsHost {
  // Returns the type of the controller class which the current handler belongs to.
  getClass<T>(): Type<T>; // Ex: PostController
  // Returns a reference to the handler (method) that will be invoked next in the request pipeline.
  getHandler(): Function; // Ex: createPost
}
```

**`Reflection and metadata`**

Custom `metadata` of route handlers can be attached through decorators created via `Reflector#createDecorator` or the built-in `@SetMetadata()` decorator

To access custom metadata, inject `Reflector` and use `get()` method

```ts
// decorator
export const SomeDecorator = Reflector.createDecorator<string[]>();

// controller
@SomeGuard(['data1'])
export class SomeController {
  @SomeGuard(['data2'])
  async create() {}
}

@Injectable()
export class SomeGuard implements CanActivate {
  constructor(private reflector: Reflector) {}
  canActive(context: ExecutionContext): boolean {
    const metadata = this.reflector.get(SomeDecorator, context.getHandler()); //[data2]
    const metadata = this.reflector.get(SomeDecorator, context.getClass()); //[data1]
    const metadata = this.reflector.getAllAndOverride(SomeDecorator, [
      context.getHandler(),
      context.getClass(),
    ]); //[data2]
    const metadata = this.reflector.getAllAndMerge(SomeDecorator, [
      context.getHandler(),
      context.getClass(),
    ]); //[data1, data2]
  }
}
```

## Others class in Request lifecycle

1. `Middleware`: Global -> Module
2. `Guards`: Global -> Controller -> Route
3. `Interceptors` (pre-controller): Global -> Controller -> Route
4. `Pipes`: Global -> Controller -> Route -> Route param
5. `Controller` (method handler)
6. `Interceptors` (post-request): Route -> Controller -> Global
7. `Exception filters`: Route -> Controller -> Global

### Middleware

> Used to modify the request and response before they are passed to the controllers. Middlewares are chained one after another until all of them run, or one ends the request life cycle.

```ts
import { Injectable, NestMiddleware } from '@nestjs/common';
import { Request, Response, NextFunction } from 'express';

@Injectable()
export class LoggerMiddleware implements NestMiddleware {
  use(req: Request, res: Response, next: NextFunction) {
    console.log('Request...');
    next();
  }
}

// use functional middleware if u dont need any dependencies
export function logger(req: Request, res: Response, next: NextFunction) {
  console.log(`Request...`);
  next();
}

export class AppModule implements NestModule {
  configure(consumer: MiddlewareConsumer) {
    consumer
      .apply(LoggerMiddleware)
      // .apply(logger) // use functional middleware
      // .apply(cors(), helmet(), logger) // multiple middeware
      .forRoutes('cats');
  }
}
```

### Exception

`HttpException`

```ts
throw new HttpException('Message', HttpStatus.FORBIDDEN); // => {statusCode, message}

throw new HttpException(
  {
    status: HttpStatus.FORBIDDEN, // => this status code can be anything u want to response to client on json
    error: 'This is a custom message',
  },
  HttpStatus.FORBIDDEN, // => this should be a valid HTTP status code
  {
    cause: error, // => (optional) not in response object, but it used for logging purposes
  },
); // => {status, error}

// custom exception
export class ForbiddenException extends HttpException {
  constructor() {
    super('Forbidden', HttpStatus.FORBIDDEN);
  }
}
throw new ForbiddenException();
```

`Exception filters`

```ts

@Catch()
export class AllExceptionFilter implements ExceptionFilter<T> {
  catch(exception: HttpException<T>, host: ArgumentHost) {
    constructor(private readonly httpAdapterHost: HttpAdapterHost) {}

    catch(exception: unknown, host: ArgumentsHost): void {
      // use httpAdapter to deliver response
      const { httpAdapter } = this.httpAdapterHost;

      const ctx = host.switchToHttp();

      const httpStatus =
        exception instanceof HttpException
          ? exception.getStatus()
          : HttpStatus.INTERNAL_SERVER_ERROR;

      const responseBody = {
        statusCode: httpStatus,
        timestamp: new Date().toISOString(),
        path: httpAdapter.getRequestUrl(ctx.getRequest()),
      };

      httpAdapter.reply(ctx.getResponse(), responseBody, httpStatus);
    }
  }
}

// only catch a specific exception: HttpException
@Catch(HttpException)
export class HttpExceptionFilter implements ExceptionFilter {
  catch(exception: HttpException, host: ArgumentsHost) {
    const ctx = host.switchToHttp();
    const response = ctx.getResponse<Response>();
    const request = ctx.getRequest<Request>();
    const status = exception.getStatus();

    response.status(status).json({
      statusCode: status,
      timestamp: new Date().toISOString(),
      path: request.url,
    });
  }
}



// method-scope
@Post()
@UseFilters(HttpExceptionFilter)
async create(@Body() createCatDto: CreateCatDto) {}

// controller-scope
@UseFilters(HttpExceptionFilter)
export class CatsController {}

// global-scope
async function bootstrap() {
  app.useGlobalFilters(new HttpExceptionFilter());
}



// inject httpAdapter
async function bootstrap() {
  const { httpAdapter } = app.get(HttpAdapterHost);
  app.useGlobalFilters(new AllExceptionsFilter(httpAdapter));
}
```

`ArgumentsHost class`

> Provides methods for retrieving the arguments being passed to a handler

Example: For Http server, host object encapsulate **[request, response, next]**. For GraphQL server, host object contains **[root, args, context, info]**

```ts
if (host.getType() === 'http') {
} else if (host.getType() === 'rpc') {
} else if (host.getType<GqlContextType>() === 'graphql') {
}
```

### Pipe

> A pipe is running just before the controller method is executed. Common use cases are validation and transformation

```ts
@Injectable()
export class ValidationPipe implements PipeTransform {
  transform(value: any, { metatype, data }: ArgumentMetadata) {
    const object = plainToInstance(metatype, value);
    const errors = await validate(object);
    if (errors.length > 0) {
      throw new BadRequestException('Validation failed');
    }
    return value;
  }
}
```

- `value` is current method argument
- `metadata`:
  - `type`: 'body' | 'query' | 'param' | 'custom'
  - `metatype`?: Type to validate
  - `data`?: the string passed to the decorator. Ex: `@CustomPipe('this is the data string')`

### Guard

> Determine whether a given request will be handled by the route handler or not. `Authorization` is usually handled by `middleware` but oen dump thing is that it doesn't know which handler will be excuted next. `Guards` have access to `ExcutionContext`, so it know exactly what's going to be excuted next.
>
> **`Guards` are executed `after` all `middleware`, but `before` any `interceptor` or `pipe`.**

```ts
// role.decorator
// attach custom metadata to route handlers
export const Roles = Reflector.createDecorator<string[]>();

//role.guard
@Injectable()
export class RolesGuard implements CanActivate {
  constructor(private reflector: Reflector) {}

  canActivate(context: ExecutionContext): boolean {
    const roles = this.reflector.get(Roles, context.getHandler());
    if (!roles) {
      return true;
    }
    const request = context.switchToHttp().getRequest();
    const user = request.user;
    return matchRoles(roles, user.roles);
  }
}

// controller
@Roles(['admin'])
async create(@Body() createCatDto: CreateCatDto) {}
```

### Interception

- Transform result / exception from controller
- Override the controller behavior for a defined route or even skip it
- Common use case: custom response, caching, logger

```ts
@Injectable()
export class LoggingInterceptor implements NestInterceptor {
  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
    // before route handler
    return next.handle().pipe(
      tap(() => {
        // after route handler
      }),
    );
  }
}
```
