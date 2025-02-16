<p align="center">
  <img src="https://raw.githubusercontent.com/acidjazz/laranuxt/main/resources/laranuxt.png" width="200" />
</p>

## Laravel + Nuxt.js Boilerplate

> Now supporting [Nuxt v3](https://v3.nuxtjs.org)


[![](https://img.shields.io/badge/nuxt.js-v3-04C690.svg)](https://v3.nuxtjs.org)
[![](https://img.shields.io/badge/Laravel-v8.73.2-ff2e21.svg)](https://laravel.com)
![Test PHP](https://github.com/acidjazz/laranuxt/workflows/Test%20PHP/badge.svg)
[![Lint Javascript](https://github.com/acidjazz/laranuxt/actions/workflows/lint-js.yml/badge.svg)](https://github.com/acidjazz/laranuxt/actions/workflows/lint-js.yml)
[![Lint PHP](https://github.com/acidjazz/laranuxt/actions/workflows/lint-php.yml/badge.svg)](https://github.com/acidjazz/laranuxt/actions/workflows/lint-php.yml)

![](resources/laranuxt.gif?raw=true)

> Examples on using Dark Mode, authentication, and listing data

### What is included

* [NUXT v3](https://v3.nuxtjs.org) front end, a progressive Vue.js framework - For Nuxt v2 visit [this branch](https://github.com/fumeapp/laranuxt/tree/nuxt2)
  * [tailvue](https://github.com/fumeapp/tailvue) a collection of components built for Nuxt.js, powered by WindiCSS|TailwindCSS
  * Authentication library to assist with user sessions and logging in/out
  * Example Authentication Middleware

* [Laravel](https://laravel.com) - for our API - `v8.60.0`
  * [Model Typer](https://github.com/fumeapp/modeltyper) - Generates Typescript interfaces from Laravel Models 
  * [MetAPI](https://github.com/acidjazz/metapi) - API helpers and utilities
  * [debugbar](https://github.com/barryvdh/laravel-debugbar) - awesome debugbar for our API
  * [ide-helper](https://github.com/barryvdh/laravel-ide-helper) - Helper files to enable help with IDE autocompletion

### Installation

* clone from GitHub
* run `yarn` and `composer install` to install all of your deps
* copy `.env.example` to `.env` and configure it to your likings
* TL;DR
 ```bash
git clone git@github.com:acidjazz/laranuxt.git; cd laranuxt; yarn; composer install; cp .env.example .env;
 ```
* Feel free to delete excess media in  `/resources/`


### Local Environment
* run `yarn dev` in one terminal for our nuxt dev setup
* run `yarn api` (alias for `./artisan serve`) in another terminal for our laravel API

### Api and Authentication

* Api and auth can be accessed via the provided `$api` library

```ts
const { $api } = useNuxtApp()
console.log($api.$user.name);
```

#### Authentication

* Take a look at [HeaderLoginModal.vue](https://github.com/fumeapp/laranuxt/blob/main/client/components/header/HeaderLoginModal.vue#L143) to see how we pass the credentials to our library
```ts
  const redirect = await $api.login(result)
  if (redirect) await router.push({path: redirect})
  $toast.show({ type: 'success', message: 'Login Successful' })
```
* Once logged on, you have the boolean `$api.loggedIn` and the object `$api.$user`
```html
  <img class="w-8 h-8 rounded-full bg-blue-400" :src="$api.$user?.avatar" alt="User Avatar">
```

#### API
The API class provides helper functions to easily retrieve, update, and remove data from your Laravel endpoints

* To get a listing/index of data, use `$api.index`
```ts
const users = $api.index<models.Users>('/user', { page: 1 })
```

* To get an individual by id, use `$api.get`
```ts
const users = $api.get<models.User>('/user/1')
```

* To update with an id, use `$api.put`
```ts
const result = $api.put<models.User>('/user/1', user)
```

* To store a new record, use `$api.store`
```ts
const result = $api.store<models.User>('/user', { name: 'Bob', email: 'bob@mail.com' })
```

* To delete with an id, use `$api.delete`
```ts
const result = $api.delete<models.User>('/user/1')
```







