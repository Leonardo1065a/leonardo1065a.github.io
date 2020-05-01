---
layout: article
title: Consumindo API, utilizando RxJS Observable e Angular
date: 2020-05-01 18:00:00-0300
coverPhoto: https://miro.medium.com/max/2560/1*kh36dX-r9FGmTYhVzC44HQ.png
---

Esse artigo tem como objetivo, te ensinar a consumir API, usando o framework <a href="https://angular.io/" target="_blank">Angular</a> e a biblioteca <a href="https://rxjs-dev.firebaseapp.com/" target="_blank">Rxjs</a>, de um modo simples, rápido e direto ao ponto.

![](https://miro.medium.com/max/2560/1*kh36dX-r9FGmTYhVzC44HQ.png)

## 1. Instalação

Vamos utilizar a plataforma <a href="https://code.visualstudio.com/" target="_blank">Visual Studio Code</a>, recomendo utilizar a mesma.

Agora vamos baixar o <a href="https://nodejs.org/en/" target="_blank">Node.js.</a>

Instale o <a href="https://cli.angular.io/" target="_blank">Angular CLI.</a>
> npm install -g @angular/cli 

Criando o projeto

> ng new crud-observable<br>
> ng cd crud-observable<br>
> ng serve<br>

O RxJS é instalado automaticamente.

Para abrirmos o projeto no Visual Studio Code, basta ir na raiz do projeto e digitar seguinte comando:

> code .

## 2. Criando componente, serviço e interface

Em primeiro lugar, vamos acessar a pasta onde iremos criar os arquivos necessários
> cd src/app/

Em segundo, criaremos o nosso componente 

> ng generate component post

Esse é o componente que iremos usar para consumir os serviços<br>

Em terceiro, nossa interface

> ng g interface resource/post resource

Essa interface servirá para tiparmos o retorno do observable

Em quarto, o serviço 

> ng generate service services/post

Esse serviço é onde nós usaremos o observable para consumir a API

## 3. Criando nossa Interface

Nossa interface, deve ser feita, de  acordo com os dados que vamos trabalhar.<br>
No typeScript, podemos tipar nossas variáveis. Sendo assim criando um contrato, que deve ser seguido.

<pre class="pure">
            export interface Post {
                userId: number;
                id: number;
                title: string;
                body: string;
            }        
</pre>

essas variáveis, foram criadas a partir da nossa <a href="https://jsonplaceholder.typicode.com/posts" target="_blank">API fake</a>

## 4. Criando os serviços

Vamos utilizar o httpClient para fazer as requisições via http<br> 
Em nosso app.module.ts, vamos importar o HttpClienteModule

<pre class="pure">
            import { HttpClientModule } from '@angular/common/http';
            imports: [ HttpClientModule ]      
</pre>

Agora estamos pronto para usar o httpClient<br>
No arquivo post.service.ts, vamos fazer a injeção dele, em nosso servico

<pre class="pure">
            import { HttpClient } from '@angular/common/http';<br>
            constructor(private http: HttpClient) { }     
</pre>

Criaremos a constante API_BASE logo abaixo dos imports<br>
Usuaremos uma API fake

<pre class="pure">
            const API_BASE = 'https://jsonplaceholder.typicode.com';    
</pre>

Ela tem a nossa URI base<br>

Agora ja podemos fazer nossa primeira requisição via http<br>

## 4.1 Result()

Criaremos um método para mapear nosso retono

<pre class="pure">
            result(){
                return pipe(
                    map((res: any) => res.json() ),
                    catchError((err: any) => throwError(err.json())),
                    map((res) => res));
            }  
</pre>

 Esse método é bem simples, usamos o pipe, para o retorno ser tratado, em seguida estamos convertando o retorno, para o formato json(). Depois fazemos a mesma coisa, caso ocorra um erro. E por fim, o retorno com map().

## 4.1 GET()

<pre class="pure">
            get(): Observable< Post[] >{
                return this.http.get(`${ API_BASE }/posts`)
                .pipe(this.result());
            }
</pre>

Na primeira linha, estamos criando nosso método get(), com um retono do tipo observable, com tipo post[]. O quer significa que nosso observable espera uma lista de post<br>
Na segunda, é nosso retono, estamos usando o http(que injetamos em nosso contrutor) http.get(), que é o tipo de requisição que será feita. Dentro do parenteses, estamos passando a URI, que retonara nosso arrays de post<br>
Depois só chamamos o metodo result() para tratar nosso retorno

## 4.2 POST()

<pre class="pure">
        post(post: Post): Observable< Post > {
            return this.http.post(`${ API_BASE }/posts`, post)
                .pipe(this.result());
        }
</pre>

Na primeira linha, estamos criando nosso método post. O observable espera o post que foi criado<br>
Na segunda, é nosso retono. http.post(), que é o tipo de requisição que será feita. Podemos perceber que estamos usando a mesma rota, porém nosso http agora é post(), e não o get(). e como um post precisa de um body, estamos passando o $post que desejamos criar<br>
Depois chamamos o result()

## 4.3 PUT()

<pre class="pure">
        put(post: Post): Observable< Post > {
            return this.http.put(`${ API_BASE }/posts/${ post.id }`, post)
            .pipe(this.result());
        }
</pre>

Na primeira linha, estamos criando nosso método put(), e como o post(), o observable espera o post que foi atualizado<br>
Na segunda, é nosso retono. Agora estamos usando o http.put(). Agora acrescentou em nossa rota um $id, que identificará o post a ser atualizado, e em seguida passamos o body, que é as novas informações do post<br>
E logo em seguinda o result()

## 4.4 DELETE()

<pre class="pure">
        delete(id: number): Observable< Object > {
            return this.http.delete(`${ API_BASE }/posts/${ id }`);
        }
</pre>

Na primeira linha, estamos criando nosso método delete(), e retona um observable do tipo Object.<br>
Na segunda, é nosso retono. Agora estamos usando o http.delete(). Como o put(), passamos em nossa rota um $id. Nese caso identificará o post a ser deletado<br>
