# nestjs-talk

Talk about [Nest Framework](https://nestjs.com) 🚀

## Contents

- [nestjs-talk](#nestjs-talk)
  - [Contents](#contents)
  - [Reference](#reference)
  - [Getting Started](#getting-started)
  - [Build & Deployment](#build--deployment)
  - [Notes](#notes)

## Reference

The Spectacle core API is available at [https://github.com/FormidableLabs/spectacle/blob/master/README.md](https://github.com/FormidableLabs/spectacle/blob/master/README.md).

## Getting Started

First, install the dependencies by running

```bash
npm install
```

Then, to start up the local server, run

```bash
npm start
```

Open a browser and hit [http://localhost:3000](http://localhost:3000), and we are ready to roll.

## Build & Deployment

Building the dist version of the project is as easy as running

```bash
npm run build
```

If you want to deploy the slideshow to surge, run

```bash
npm run deploy
```

## Notes

### Introduction

#### Constat

Beaucoup de frameworks et libs côté front-end ont permis aux développeurs d'être plus productifs, tout en construisant des applications plus volumineuses et élégantes, sans que leurs performances ne soient lésées.

Côté back-end avec NodeJS, les frameworks les plus utilisés ne proposent pas de solutions aux principaux besoins récurrents des développeurs, il faut piocher dans l'écosystème pour trouver son bonheur et quand des problèmes surviennent pour faire évoluer l'architecture, les développeurs se retrouvent seuls face à npm.

Il existe bien des frameworks comme loopback qui proposent de répondre aux besoins récurrents des développeurs mais leur approche et leur configuration peuvent faire peur et mener à la création d'usine à gaz magiques.

#### Problèmes rencontrés

- des contrôleurs de partout dans une arborescense de fichiers non normée
- difficilement testable (extraction des callback dans des méthodes de classe dédiée + utilisation async await) mais pas suffisant à notre goût (toujours req, res, app...)
- on confie tout aux middlewares (auth, logging, data transformation & validation, exception handlers...)
- absence typage, couplage fort avec un framework particulier,

**Heureusement, NestJS est arrivé !!!**

### NestJS

#### Présentation générale

- Opinionated (cette-fois ci) et tiré du monde d'Angular + Spring Boot (pour les adeptes, vous devriez reconnaître un bon nombre de similitude ;))
- Basé sur Express
- Ecrit en Typescript
- SOLID principles
- Propose une réelle architecture applicative et simplifie la mise en place du DDD
- Finit le couplage fort à Express (req, res, app) + mocking de ces objets dans les tests
- Simples fonctions indépendantes de toute couche de transport (HTTP, Websocket) et facilement testables retournant val sync (promise) ou async (observable)

**Très bien, voyons ce qu'apporte réellement Nest en terme de productivité, de maintenabilité, de testabilité et d'architecture...**

### Concepts

#### Controllers

- Décorateurs : @Controller, @Get() etc...
- Codes de retour paramétrables
- Récupération des objets req res via des décorateurs si besoin...

#### Providers

- Presque tout est considéré comme un provider (service, repo, facotry, helper etc...)
- Ils peuvent tous être injectés dans des constructeurs
- Décorateur: @Injectable
- Injection de dépendances (private readonly myService: MyService)

#### Modules

- Fidèle aux principes de conception [SOLID](https://en.wikipedia.org/wiki/SOLID) et de [séparation des intérêts](https://en.wikipedia.org/wiki/Separation_of_concerns)
- 4 propriétés : imports, controllers, providers et exports

#### Middlewares

- Fonction appelé avant le route handler. Ils ont accès à la requête, à la réponse et au prochain middleware (req, res, next)
- implements NestMiddleware avec une fonction resolve qui retourne une MiddlewareFunction
- Tiré de la doc d'express : "Middleware functions can perform the following tasks:"
  - execute any code.
  - make changes to the request and the response objects.
  - end the request-response cycle.
  - if the current middleware function does not end the request-response cycle, it must call next() to pass control to the next middleware function. Otherwise, the request will be left hanging.
- Application des middlewares dans un module via la méthode configure(consumer: MiddlewareConsumer)

#### Exception filters

- gestion des exceptions lancées par l'appli
- finit les res.status(404).end() ni d'utilisation de boom, il y a des built-in classes NotFoundException, ForbiddenException etc... (throw new NotFoundException())
- Création de ses propres exceptions également en étendant HttpException
- ces filtres sont éxécutés en fin de chaîne pour le type d'exception qu'on leur attribue (si aucun spécifié => pour toutes les exceptions de l'appli)
- Décorateur @Catch(NotFoundException)
- Class qui implémente Exceptionfilter avec une méthode catch(exception: HttpException, host: ArgumentsHost)
- Utile pour logger dès lors qu'une exception survient.

#### Pipes

- Transformation et validation d'une donnée d'entrée
- ValidationPipe, ParseIntPipe... (built-in)
- Création de ses propes pipes
- ValidationPipe permet de valider en fonction d'un modèle défini en amont avec des décorateurs sur les propriétés du DTO (@IsString() @IsNotEmpty() @MinLength(10) etc...)
- Par défaut, si le corps de la requête (d'où le décorateur @Body()) ne correspond pas au modèle attendu, ValidationPipe interrompt le cycle Requête-Réponse pour lancer une BadRequestException (aka Error 400).

#### Testing

Extrait de l'[article](https://vinceops.me/2018/08/03/nest-le-framework-node-js-quil-nous-fallait/#introduction) de vinceops :

Le test des composants est très largement facilité par :

- L'injection de dépendance : toute dépendance peut être facilement simulée (mock) avant d'être passée en paramètre d'un constructeur.
- L'aspect framework agnostic de Nest : il n'y a pas (ou rarement) besoin de mock des objets propres à Express (req, res...) et toute dépendance externe (package npm, etc) peut aussi être injectée à l'aide d'un custom provider.

Le test de l'application (End-to-End) est quelque peu facilité par la modularité de celle-ci. En effet, Nest fournit un TestingModule dans lequel on importe un à plusieurs modules pour lancer notre application.
Grâce (encore) aux custom providers, on peut, au besoin, remplacer certains composants : par exemple, pour tester différentes implémentations d'une interface ILambdaService (AWS Lambda, Google Cloud Functions, ...) ou deux états d'une même implémentation (service disponible et service hors-ligne/en maintenance).

### Pour les curieu(x|ses)...

- Interceptors
- Guards
- GraphQL
- OpenAPI (Swagger)
- Microservices
- Websockets
- Authentication
- Execution context (CLI, scripting...)

### Conclusion

En résumé, Nest est un cadre structurant encourageant les bonnes pratiques, framework agnostique, portant une architecture modulaire, offrant de l'injection de dépendances et de puissants composants, accompagné d'intégrations élégantes, le tout écrit avec/pour TypeScript.

## Liens utiles (merci à eux)

- https://docs.nestjs.com
- https://github.com/FormidableLabs/spectacle
- https://vinceops.me/2018/08/03/nest-le-framework-node-js-quil-nous-fallait/
