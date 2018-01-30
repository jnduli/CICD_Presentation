%title: CI/CD - Presentation %author: jnduli.co.ke 
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

Competitor to github
Pretty cool place to work from
I get 20000 minutes CI/CD so its ok free projects

******

-> # Basics # <-
To start with ci/cd on gitlab add a .gitlab-ci.yml file at the
root of the repository. The simplest ci/cd is of the form:

<!-- TODO: Example that prints hello world -->

-> # JS ci/cd example <-
Here is the file we use for our front end:

<!-- TODO: include ci/cd file for front end  -->

-> # Django Backend Example <-
Here is what we use for our back end

<!--TODO: include ci/cd for backend  -->

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


