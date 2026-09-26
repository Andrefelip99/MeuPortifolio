# André Felipe — Portfólio

Portfólio pessoal desenvolvido para apresentar meu trabalho com desenvolvimento de software. A interface combina uma identidade visual inspirada em laboratórios digitais com uma cena 3D interativa e estudos de projetos reais.

## Projetos em destaque

### JC Decorar

API REST para gerenciamento de projetos de decoração, desenvolvida com Java e Spring Boot. O projeto inclui operações de cadastro, consulta, atualização e remoção, além de autenticação e persistência PostgreSQL.

- [Abrir projeto](https://jc-decorar-site.vercel.app/)
- Tecnologias documentadas: Java 21, Spring Boot, Spring Security, JWT, PostgreSQL e Docker.
- Capturas de tela: `JcDecorar/`

### Macedo Farias

Catálogo digital de produtos de confeitaria, com busca, categorias e área administrativa. A aplicação separa frontend e backend e integra serviços para banco de dados e armazenamento de imagens.

- [Abrir projeto](https://macedofarias.vercel.app/#/)
- Tecnologias documentadas: Vue.js, Java, Spring Boot, PostgreSQL, Cloudinary e GSAP.
- Capturas de tela: `MacedoFarias/`

## Experiência do portfólio

- Cena 3D criada com Three.js, com iluminação, geometria, movimento sutil e resposta ao cursor.
- Animações de entrada e revelação ao rolar a página com GSAP e ScrollTrigger.
- Rolagem suave com Lenis, desativada quando o sistema prefere movimento reduzido.
- Galerias de projeto com duas capturas, navegação por setas e gesto de arrastar.
- Layout adaptado para telas menores e alternativa visual caso a criação do contexto WebGL falhe.

## Tecnologias

- Vue 3 e Composition API
- Vite
- JavaScript, HTML e CSS
- Three.js
- GSAP e ScrollTrigger
- Lenis

## Executar localmente

Requer Node.js e npm instalados.

```bash
npm install
npm run dev
```

O Vite exibirá no terminal o endereço local para abrir no navegador.

Para gerar e visualizar a versão de produção:

```bash
npm run build
npm run preview
```

Os arquivos finais são gerados na pasta `dist/`.

## Estrutura

```text
.
├── JcDecorar/             # Capturas e informações do projeto JC Decorar
├── MacedoFarias/          # Capturas e informações do projeto Macedo Farias
├── src/
│   ├── App.vue            # Conteúdo, dados dos projetos e cena interativa
│   ├── main.js            # Inicialização do Vue
│   └── style.css          # Estilos e responsividade
├── index.html
├── vite.config.js
└── package.json
```

## Publicação

O portfólio é uma aplicação frontend estática. Gere a pasta de produção com `npm run build` e publique o conteúdo de `dist/` em um serviço compatível com sites estáticos, como Vercel ou Netlify.

## Desenvolvedor

**André Felipe** — estudante de Análise e Desenvolvimento de Sistemas, com interesse em desenvolvimento backend e aplicações web.
