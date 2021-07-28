# How to start a python package?
Starting a python package might be overwhelming to newbiees, which was the case for me. At the begining I was following the skeleton/folder structures from some of the published python packages from my mentors or lab- which is a good way to start. Even better will be use the tools such as cookie-cutter to to structure the architecure(skeleton) for a python package. There are many great cookie-cutter template with different features. Depending on your need such as template for conda-recipe, template for Cicle-CI or Travis ...etc, you can search for appropriate cookie-cutter template. For GuideMaker, I use the template from conda (https://github.com/conda/cookiecutter-conda-python). You can follow the setup steps as decribed in the repo. During the setup, you can provide various meta data for the package. 

```bash
# install cookiecutter
$ conda install cookiecutter

# download cookiecutter-conda-python template 
$ cookiecutter https://github.com/conda/cookiecutter-conda-python.git

# Once cookiecutter clones the template, you will be asked a series of questions related to your project

$ full_name [Full Name]: Enter your full name.

$ email [Email Address]: Enter your email address

$ github_username [github_username]: Enter your github username

$ repo_name [repository_name]: Enter the name of your project's repository

$ package_name [package_name]: Enter the name of your package

$ application_name [application]: Enter the name of your GUI application

$ project_short_description [Short description]: Enter a short description about your project


```

