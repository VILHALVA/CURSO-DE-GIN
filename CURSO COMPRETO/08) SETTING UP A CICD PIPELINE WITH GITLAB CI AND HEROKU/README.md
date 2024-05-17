# SETTING UP A CICD PIPELINE WITH GITLAB CI AND HEROKU
Configurar um pipeline CI/CD com GitLab CI e Heroku é uma ótima maneira de automatizar o processo de entrega contínua para suas aplicações hospedadas no Heroku. Aqui está um guia passo a passo para configurar esse pipeline:

## 1. Preparar o Projeto no GitLab
1. **Crie um Repositório no GitLab**: Se você ainda não tiver um, crie um repositório para o seu projeto no GitLab.

2. **Adicione o Código Fonte**: Faça o upload do código fonte do seu projeto para o repositório no GitLab.

## 2. Configurar Variáveis de Ambiente no GitLab
1. **Acesse as Configurações do Projeto**: No seu projeto GitLab, vá para `Settings` > `CI / CD` > `Variables`.

2. **Adicione as Variáveis de Ambiente do Heroku**: Adicione as variáveis de ambiente necessárias para autenticação no Heroku, como `HEROKU_API_KEY`, `HEROKU_APP_NAME`, etc.

## 3. Crie o Arquivo de Configuração CI/CD
1. **Crie o Arquivo `.gitlab-ci.yml`**: Na raiz do seu projeto, crie um arquivo chamado `.gitlab-ci.yml` e configure as etapas do pipeline CI/CD.

## 4. Configure as Etapas do Pipeline
Aqui está um exemplo básico de um arquivo `.gitlab-ci.yml` para configurar um pipeline CI/CD para implantar no Heroku:

```yaml
image: docker:stable

stages:
  - build
  - deploy

variables:
  DOCKER_DRIVER: overlay2
  HEROKU_APP_NAME: "seu-app-no-heroku"

services:
  - docker:dind

before_script:
  - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY

build:
  stage: build
  script:
    - docker build -t $CI_REGISTRY_IMAGE .
    - docker push $CI_REGISTRY_IMAGE

deploy:
  stage: deploy
  script:
    - docker pull $CI_REGISTRY_IMAGE
    - docker login --username=_ --password=$HEROKU_API_KEY registry.heroku.com
    - docker tag $CI_REGISTRY_IMAGE registry.heroku.com/$HEROKU_APP_NAME/web
    - docker push registry.heroku.com/$HEROKU_APP_NAME/web
    - heroku container:release web -a $HEROKU_APP_NAME
  only:
    - master
```

## 5. Testar o Pipeline
Após configurar o arquivo `.gitlab-ci.yml`, faça um commit e envie para o repositório GitLab. O GitLab CI/CD irá automaticamente iniciar o pipeline e executar as etapas de build e deploy.

## 6. Monitorar o Pipeline
Você pode monitorar o progresso do pipeline na seção `CI/CD` do seu projeto no GitLab. Verifique se todas as etapas estão passando com sucesso e a aplicação foi implantada no Heroku.

Com esse pipeline CI/CD configurado, todas as mudanças feitas no código fonte e enviadas para o repositório GitLab serão automaticamente construídas e implantadas no Heroku, facilitando o processo de entrega contínua. Certifique-se de ajustar as configurações e as etapas conforme necessário para atender aos requisitos específicos do seu projeto.