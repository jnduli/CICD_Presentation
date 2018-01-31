%title: CI/CD - Presentation 
%author: jnduli.co.ke 

-> # CI/CD # <-

Presenter: John Nduli

******

-> # What is CI/CD # <-

Continuous Integration, Continuous Deployment and Continuous
Delivery.

It is provided by the following platforms:

+ Bitbucket : Has an inbuilt ci cd.
+ GIthub : Travis CI most popular but can work with other tools
+ Gitlab: Has inbuilt ci cd tool

We'll focus on Gitlab today

******

-> # Gitlab # <-

Gitlab CI considered leader in CI/CD in 
[Forrester Wave evaluation](https://reprints.forrester.com/#/assets/2/921/RES137261/reports)
It started as a competitor to github
I get 20000 minutes CI/CD so its ok free projects
Anyone remember their huge db backup fail last year ??
[incident](https://about.gitlab.com/2017/02/01/gitlab-dot-com-database-incident/)

******

-> # Basics # <-
To start with ci/cd on gitlab add a .gitlab-ci.yml file at the
root of the repository. The simplest ci/cd is of the form:

~~~

    image: alpine

    Hello:
      script:
        - echo "Hello World"

~~~

******

-> # JS ci/cd example <-
Here is the file we use for our front end:

~~~
image: node:9.4.0

test:
  stage: test
  script:
    - npm install
    - npm run unit

build site:
  stage: build
  script:
    - npm install --progress=false
    - npm run build
  only:
    - develop
  artifacts:
    expire_in: 1 week
    paths:
      - dist

deploy:
  image: alpine
  stage: deploy
  script:
    - apk add --no-cache rsync openssh
    - eval $(ssh-agent -s)
    - echo "$SSH_PRIVATE_KEY" | tr -d '\r' | ssh-add - > /dev/null
    - mkdir -p ~/.ssh
    - chmod 700 ~/.ssh
    - ssh-keyscan jnduli.co.ke >> ~/.ssh/known_hosts
    - chmod 644 ~/.ssh/known_hosts
    - '[[ -f /.dockerenv ]] && echo -e "Host *\n\tStrictHostKeyChecking no\n\n" > ~/.ssh/config'
    - rsync -rav --delete dist/ rookie@jnduli.co.ke:/home/rookie/projects/dev_mradi_frontend
  only: 
    - develop
~~~

******

-> # Django Backend Example <-
Here is what we use for our back end

~~~
    image: python:3.6

    test:
      stage: test
      script:
        - apt-get update -qy
        - apt-get install -y python-dev python-pip
        - pip install -r requirements.txt
        - python manage.py makemigrations
        - python manage.py migrate
        - python manage.py test

    deploy:
      image: alpine
      stage: deploy
      script:
        - apk add --no-cache openssh ansible
        - eval $(ssh-agent -s)
        - echo "$SSH_PRIVATE_KEY" | tr -d '\r' | ssh-add - > /dev/null
        - mkdir -p ~/.ssh
        - chmod 700 ~/.ssh
        - ssh-keyscan jnduli.co.ke >> ~/.ssh/known_hosts
        - chmod 644 ~/.ssh/known_hosts
        - '[[ -f /.dockerenv ]] && echo -e "Host *\n\tStrictHostKeyChecking no\n\tForwardAgent yes\n\n" > ~/.ssh/config'
        - ansible-playbook -i 'jnduli.co.ke,' -u 'rookie' --extra-vars="ansible_become_pass=$ROOKIE_PASSWORD" ansible/basic_deploy.yml
      only: 
        - develop
~~~



*****

-> # Bitbucket Example <-
Bitbucket ci/cd framework is different from the gillab one. Some
of the differences are:

+ Automatic ssh integration into pipeline ( This is both a good
  and a bad thing)
+ Specifying branch specific tasks
+ No support for artifacts
+ Monthly limit of 50 minutes vs gitlab's 20000 minutes

*****

-> # Example on latex compilation <-

~~~
    image: tianon/latex

    pipelines:
      branches:
        structured:
          - step:
              script:
                - apt-get update && apt-get install -y rsync openssh-server
                - cd src/John
                - pdflatex ndulicv.tex
                - ls
                - eval $(ssh-agent -s)
                - rsync ndulicv.pdf rookie@jnduli.co.ke:/home/rookie/projects/cv/
~~~


