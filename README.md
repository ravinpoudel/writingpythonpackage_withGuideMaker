# How to start a python package?
Starting a python package might be overwhelming to newbiees, which was the case for me. At the begining I was following the skeleton/folder structures from some of the published python packages from my mentors or lab- which is a good way to start. Even better will be use the tools such as cookie-cutter to to structure the architecure(skeleton) for a python package. There are many great cookie-cutter template with different features. Depending on your need such as template for conda-recipe, template for Cicle-CI or Travis ...etc, you can search for appropriate cookie-cutter template. For GuideMaker, I use the [template from conda](https://github.com/conda/cookiecutter-conda-python). You can follow the setup steps as decribed in the repo. During the setup, you can provide various meta data for the package. 


## cookiecutter
```
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


Following is the folder tree for repo_name: `GuideMaker` where package_name: `guidemaker`.

```
(myenv) 🙏 tree -a
.
├── .circleci
│   └── config.yml
├── .codacy.yml
├── .coveragerc
├── .gitattributes
├── .gitignore
├── .travis.yml
├── LICENSE
├── MANIFEST.in
├── README.rst
├── appveyor.yml
├── conda.recipe
│   └── meta.yaml
├── guidemaker
│   ├── __init__.py
│   ├── __main__.py
│   ├── _version.py
│   └── cli.py
├── setup.cfg
├── setup.py
├── tests
│   ├── __init__.py
│   └── test_cli.py
└── versioneer.py

```

## setup.py

What is setup.py?
The setup script is the centre of all activity in building, distributing, and installing modules using the Distutils. The presence of this file indicats that the module/package you are about to install has likely been packaged and distributed with Distutils, which is the standard for distributing Python Modules. Additionally, this file allows one to define the package requirement as well as the metadata about the package.

Lets look little in detail on file `setup.py`.

```
from setuptools import setup
import versioneer

requirements = [
    # package requirements go here
]

setup(
    name='GuideMaker',
    version=versioneer.get_version(),
    cmdclass=versioneer.get_cmdclass(),
    description="Software to design CRISPR-Cas guide RNA pools in non-model genomes",
    license="MIT",
    author="Ravin Poudel",
    author_email='rp3448@ufl.edu',
    url='https://github.com/ravinpoudel/GuideMaker',
    packages=['guidemaker'],
    entry_points={
        'console_scripts': [
            'guidemaker=guidemaker.cli:cli'
        ]
    },
    install_requires=requirements,
    keywords='GuideMaker',
    classifiers=[
        'Programming Language :: Python :: 2.7',
        'Programming Language :: Python :: 3.6',
        'Programming Language :: Python :: 3.7',
    ]
)

```


Following is how we modify `setup.py` file for GuideMaker.

```
"""setup.py: python package setup for GuideMaker
"""

from setuptools import setup
import versioneer

requirements = [
    # package requirements go here
    'biopython==1.76',
    'numpy >=1.11',
    'pybedtools>=0.8.2',
    'nmslib>=2.0.6',
    'pandas>=1.0.0',
    'pyyaml>=5.4.1',
    'regex==2020.11.13',
    'altair',
    'streamlit>=0.81.0',
    'pytest>=4.6',
    'pytest-cov',
    'streamlit_tags>=1.2.6',
    'pdoc3'
]



setup(
    name='guidemaker',
    version=versioneer.get_version(),
    cmdclass=versioneer.get_cmdclass(),
    description='GuideMaker: Software to design gRNAs pools in non-model genomes and CRISPR-Cas',
    license='CC0 1.0 Universal (CC0 1.0) Public Domain Dedication',
    author='Adam Rivers',
    author_email='adam.rivers@usda.gov',
    url='http://tinyecology.com',
    long_description=open('README.md').read(),
    packages=['guidemaker'],
    entry_points={
        'console_scripts': [
            'guidemaker=guidemaker.cli:main'
        ]
    },
    install_requires=requirements,
    python_requires='>=3.6',
    test_suite='pytest',
    tests_require=['pytest'],
    keywords='CRISPR-Cas',
    include_package_data=True,
    zip_safe=False,
    classifiers=[
        'Programming Language :: Python :: 3.7',
        'Programming Language :: Python :: 3.8',
        'Development Status :: 3 - Alpha'
    ]
)


```

## setup.cfg
What is setup.cfg? What it does? How to use it?

setup.py is the file with the actual instructions how to build your software. These instructions might have some configuration options, e.g. for unit tests you might be able to indicate whether test coverage should be computed or not, or the install prefix etc.

setup.cfg is a file which might be used to specify such options in addition to reading the command line when calling python setup.py <somecommand>.
    
[Documentation of setup.cfg](https://docs.python.org/2.7/distutils/configfile.html)

Often, it’s not possible to write down everything needed to build a distribution a priori: you may need to get some information from the user, or from the user’s system, in order to proceed. As long as that information is fairly simple—a list of directories to search for C header files or libraries, for example—then providing a configuration file, setup.cfg, for users to edit is a cheap and easy way to solicit it. Configuration files also let you provide default values for any command option, which the installer can then override either on the command-line or by editing the config file.

    
```bash
[flake8]
max-line-length = 100
ignore = E122,E123,E126,E127,E128,E731,E722
exclude = build,guidemaker/_version.py,tests,conda.recipe,.git,versioneer.py,benchmarks,.asv

[tool:pytest]
norecursedirs= .* *.egg* build dist conda.recipe
addopts =
    --cov=guidemaker --cov-report xml
    --ignore setup.py
    --ignore run_test.py
    --cov-report term-missing
    --tb native
    --strict
    --durations=20
env =
    PYTHONHASHSEED=0
markers =
    serial: execute test serially (to avoid race conditions)

[versioneer]
VCS = git
versionfile_source = guidemaker/_version.py
versionfile_build = guidemaker/_version.py
tag_prefix =
parentdir_prefix = GuideMaker-

[bdist_wheel]
universal=1

```



## MANIFEST.in
What is MANIFEST.in? What it does? How to use it?

When building a source distribution for your package, by default only a minimal set of files are included. You may find yourself wanting to include extra files in the source distribution, such as an authors/contributors file, a docs/ directory, or a directory of data files used for testing purposes. There may even be extra files that you need to include; for example, if your setup.py computes your project’s long_description by reading from both a README and a changelog file, you’ll need to include both those files in the sdist so that people that build or install from the sdist get the correct results.

Adding & removing files to & from the source distribution is done by writing a MANIFEST.in file at the project root.

Lets look more into another file `MANIFEST.in`


```
include versioneer.py

```

Following is how we modify `MANIFEST.in` file for GuideMaker.


```
include versioneer.py
include guidemaker/_version.py
include tests/test_data/*
include LICENSE.txt
include README.md
include guidemaker/data/*
include guidemaker/data/Pseudomonas_aeruginosa.gbk.gz
include guidemaker/data/Carsonella_ruddii.gbk.gz
```



Now you are ready to install your python package!!!

```bash
# cd to the folder where you have setup.py file
(myenv) 🙏 pip install .

(myenv) 🙏 guidemaker -h
```
Congratulations!!!! 

This is how you can get started. Now, you need to add more functions to your packages. The actual code that has been implented for `__main__.py` and `__cli__.py` are available [here](https://github.com/USDA-ARS-GBRU/GuideMaker/tree/main/guidemaker)

## Pytest
What is Pytest? What it does? How to use it?
```bash
(myenv) 🙏 pip install pytest-cov
(myenv) 🙏 pytest


---------- coverage: platform darwin, python 3.7.10-final-0 ----------
Name                     Stmts   Miss  Cover   Missing
------------------------------------------------------
guidemaker/__init__.py       3      0   100%
guidemaker/cli.py           10      2    80%   27-28
------------------------------------------------------
TOTAL                       13      2    85%
Coverage XML written to file coverage.xml

======================================================== slowest 20 durations =========================================================

(3 durations < 0.005s hidden.  Use -vv to show these durations.)
==================================================== 1 passed, 2 warnings in 0.11s ====================================================
```
Example of writing pytests for functions in the softare is available [here](https://github.com/USDA-ARS-GBRU/GuideMaker/tree/main/tests).


## circle ci

CircleCI is a modern continuous integration and continuous delivery (CI/CD) platform. CircleCI automates build, test, and deployment of software.CircleCI is a cloud-based CI/CD tool that automates installation and delivery procedures. It offers quick configuration and maintenance without any complexities. Since it is a cloud-based CI/CD tool, it eliminates the redundancy of a dedicated server and cuts down the cost of maintenance of a constant local server host. Moreover, the cloud-based server plans are scalable, robust, and facilitate faster deployment of applications.

Continuous integration is a development approach where programmers merge their code into a shared repository, which is further verified by automated test and build sequences.Continuous integration focuses on test data and automatic system deployment to smoothen the integration process. 

Continuous delivery is a software development process wherein the development phase is more streamlined to allow fast and efficient deliveries to production. A successful continuous delivery method requires ensuring that it can always be in a state where it can be immediately deployed. 
                     
```
version: 2
jobs:
  build:
    docker:
      - image: continuumio/miniconda3:4.9.2

    working_directory: ~/repo

    steps:
      - checkout

      
      # - restore_cache:
      #     keys:
      #     - v1-dependencies-{{ checksum "environment.yml" }}
      #     - v1-dependencies-
      

      - run:
          name: install dependencies
          command: |
            # conda env create -q || conda env update -q
            # source activate adj
            conda install -qy conda-build anaconda-client pytest pytest-cov
            conda install conda-verify
            conda config --set auto_update_conda no
            conda config --add channels conda-forge
            conda config --add channels bioconda
            # conda config --add channels gagan3012
            conda info -a
            conda build conda.recipe --no-test
            conda install --use-local guidemaker

      
      # - save_cache:
      #     paths:
      #       - /opt/conda
      #     key: v1-dependencies-{{ checksum "environment.yml" }}
      

      - run:
          name: run tests
          command: |
            # source activate adj
            pytest --color=yes -v --cov=guidemaker tests
            conda install -c conda-forge codecov
            codecov

      - store_artifacts:
          path: test-reports
          destination: test-reports

```


## coverage 
Code coverage is the percentage of code which is covered by automated tests. Code coverage measurement simply determines which statements in a body of code have been executed through a test run, and which statements have not. In general, a code coverage system collects information about the running program and then combines that with source information to generate a report on the test suite's code coverage.
[More information @](https://openclover.org/doc/manual/4.2.0/general--about-code-coverage.html)
    
Github repo can be linked with [codecov](https://about.codecov.io), which can generate a nice html summary using `coverage.xml` generated by pytest. Check `setup.cfg` file to see the steps that goes to generate `coverage.xml` file.

## build and test within github
With gihub action and workflow, it is possible to test whether the software pass the bulild and unit test for a selected version of operating systems or selected version of software such as python. Once can even run a matrix of various OS and python version to test the build and pytest. Similar to CircleCI, it will check softoware prior publication and check for the specified versions. 

## GitHub actions and workflow

## How to build a docker?

## How to create a package in gitHub?

## CD/CI with workflows and actions file

## Automate python package creation
- manage package access

## Release a version of the software

## Put docker images in github registry

## Automate deployment to AWS Fargate
- resource setup
- how to do this for USDA
                     
```
########################FINALIZE STEPS#########
(awscli2) 🙏 cd /Users/admin/Documents/GITHUB_TOKEN/aws-saml-scinet
(awscli2) 🙏 ./aws_saml_scinet.py 
(awscli2) 🙏 cd /Users/admin/Documents/GuideMaker_ALL/GuideMaker/docker-images/webapp
(awscli2) 🙏 docker build -f Dockerfile -t webapp .
# check if app runs properly --locally using docker images
(awscli2) 🙏 docker run -p 8501:8501 webapp
(awscli2) 🙏 docker tag webapp 720171569227.dkr.ecr.us-east-1.amazonaws.com/guidemakerapp
(awscli2) 🙏 docker login -u AWS -p $(aws ecr get-login-password --region us-east-1 --profile saml) 720171569227.dkr.ecr.us-east-1.amazonaws.com
(awscli2) 🙏 docker push 720171569227.dkr.ecr.us-east-1.amazonaws.com/guidemakerapp
                      
                     
```

## AWS web deployment
 - Route 53
 - Load balancer
 - Auto scaling
 - Linking DNS name from load balancer to domain name via route 53
 - Adding SSL cerficate
 
## Bioconda recipe
- pull request
- Need to make comments prior so that someone will start looking your code for branch merge

## How to develop a web app using streamlit?

