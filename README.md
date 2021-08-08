# How to start a python package?
Starting a python package might be overwhelming to newbies, which was the case for me. In the beginning, I was following the skeleton/folder structures from some of the published python packages from my mentors or lab- which was a good way to start. Even better will be using the tools such as cookie-cutter to structure the architecture(skeleton) for a python package. There are many excellent cookie-cutter templates with different features- such as the option to generate YAML files for CircleCI, Travis CI, Conda recipe, etc. For GuideMaker, I use the [template from conda](https://github.com/conda/cookiecutter-conda-python). You can follow the setup steps as described in the repo. During the setup, you can provide various metadata for the package as well.


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
setup.py is the file with the actual instructions on how to build the software. These instructions might have some configuration options, e.g. for unit tests you might be able to indicate whether test coverage should be computed or not, or the install prefix etc. Additionally, this file allows one to define the package requirement as well as the metadata about the package.

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

setup.cfg is also a package configuration file. This file allows adding additional information or steps/commands needed while building the package. Instructions in `setup.cfg` are read by `setup.py` and executed while installing a package with pip install.
  
[Documentation of python configuration files](https://docs.python.org/2.7/distutils/configfile.html)

    
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
When building a source distribution for your package, by default only a minimal set of files are included. You may find yourself wanting to include extra files in the source distribution, such as an authors/contributors file, a docs/ directory, or a directory of data files used for testing purposes. There may even be extra files that you need to include; for example, if your setup.py computes your project’s long_description by reading from both a README and a changelog file, you’ll need to include both those files in the sdist so that people that build or install from the sdist get the correct results.
Let's look more into another file `MANIFEST.in`

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

This is how you can get started. Now, you need to add more functions to your packages. The actual code that has been implemented for `__main__.py` and `__cli__.py` are available [here](https://github.com/USDA-ARS-GBRU/GuideMaker/tree/main/guidemaker)

## Pytest
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
An example of writing pytest for functions in the software is available [here](https://github.com/USDA-ARS-GBRU/GuideMaker/tree/main/tests).


## [CircleCI](https://app.circleci.com/pipelines/github/USDA-ARS-GBRU/GuideMaker)

CircleCI is a modern continuous integration and continuous delivery (CI/CD) platform. CircleCI automates the build, test, and deployment of software.CircleCI is a cloud-based CI/CD tool that automates installation and delivery procedures. It offers quick configuration and maintenance without any complexities. Since it is a cloud-based CI/CD tool, it eliminates the redundancy of a dedicated server and cuts down the cost of maintenance of a constant local server host. Moreover, the cloud-based server plans are scalable, robust, and facilitate faster deployment of applications.

Continuous integration is a development approach that allows automated test and build sequences. Continuous integration focuses on test data and automatic system deployment to smoothen the integration process. 
                     
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


## [Coverage](https://app.codecov.io/gh/USDA-ARS-GBRU/GuideMaker) & [Code Quality](https://www.codacy.com/gh/USDA-ARS-GBRU/GuideMaker/dashboard?utm_source=github.com&utm_medium=referral&utm_content=USDA-ARS-GBRU/GuideMaker&utm_campaign=Badge_Grade)
Code coverage is the percentage of code that is covered by automated tests. Code coverage measurement simply determines which statements in a body of code have been executed through a test run, and which statements have not. In general, a code coverage system collects information about the running program and then combines that with source information to generate a report on the test suite's code coverage.
[More information @](https://openclover.org/doc/manual/4.2.0/general--about-code-coverage.html)
    
Github repo can be linked with [codecov](https://about.codecov.io), which can generate a nice HTML summary using `coverage.xml` generated by pytest. Check `setup.cfg` file to see the steps that go to generate `coverage.xml` file.


## GitHub Workflow and Actions
GitHub workflow and actions are tools that allow for CD/CI. Workflow is YAML file that consists of various actions that we want to run when we push the commit or release the new version of the software. For example, whenever we release the software, we want to update the docker version of the software or test whether the software passes build and test, or in our case update the docker container at AWS that is used for the web application. It's tedious and not efficient to manually updates files at different locations. Rather these can be set up in the workflow and actions files. With the workflow file, one can define when to trigger the actions, such as either on `commit`, or on `release`,  and so on.

With GitHub action and workflow, it is also possible to test whether the software passes the build and unit test for a selected version of operating systems or a selected version of software such as python, similar to CircleCI. One can even run a matrix of various OS and python versions to test the build and pytest. 
    
**How to write Workflow and define Actions?**
    
Often you can find these workflow and actions file templates at [Github market place](https://github.com/marketplace?type=). You can even write your own file or also use the template from others as well. 


## How to build a docker image for GitHub Registry?
Docker containers can be built and deploy at the docker hub and other avenues. Here for GuideMaker, we want to publish it in [github regirsty](https://github.com/features/packages) so that everything is maintained at GitHub, and overcome some of the limitations of docker hub. The process of creating a container image is similar to the docker hub i.e. by using the docker file. Once we have docker file[For GuideMaker we have three-- in the workflow file -- check the line: context: docker-images/avx, which specify the particular docker file], we can easily trigger the docker build and publishing to GitHub registry using workflow and actions. Before doing that, we need to obtain developer authorization and add this to the `SECRETS` with the code repo. If you check the following workflow file then `github.repository_owner` and `MY_GITHUB_RESISTRY_TOKEN` are the two information that you need to set up for your repo. Within the repo go to the setting, then to the `SECRETS`, then add the authentication information. You don't have to do anything for `github.repository_owner` as this will be default GitHub user, but need to add a token for `MY_GITHUB_RESISTRY_TOKEN` - go to profile > Settings> Developer Settings > Personal access tokens.
    
    
**Notes:** When the docker image is published, it will be available under `Packages` section of your Github home page. You can modify the visibility settings as well as linked the published images to the respective repo.


```text
name: Publish Docker image
on:
  release:
    branches: main

jobs:
  build-and-push-image:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout
      uses: actions/checkout@v2

    - name: Get short SHA
      id: slug
      run: echo "::set-output name=sha12::$(echo ${GITHUB_SHA} | cut -c1-12)"

    - name: Login to GitHub Container Registry
      uses: docker/login-action@v1
      with:
        registry: ghcr.io
        username: ${{ github.repository_owner }}
        password: ${{ secrets.MY_GITHUB_RESISTRY_TOKEN }}

    - name: Build AVX container image and push to ghcr
      uses: docker/build-push-action@v2
      with:
        context: docker-images/avx
        push: true
        tags: ghcr.io/usda-ars-gbru/guidemaker-avx:sha-${{ steps.slug.outputs.sha12 }}

    - name: Build NON-AVX container image and push to ghcr
      uses: docker/build-push-action@v2
      with:
        context: docker-images/nonavx
        push: true
        tags: ghcr.io/usda-ars-gbru/guidemaker-nonavx:sha-${{ steps.slug.outputs.sha12 }}

    - name: Build WEBAPP container image and push to ghcr
      uses: docker/build-push-action@v2
      with:
        context: docker-images/webapp
        push: true
        tags: ghcr.io/usda-ars-gbru/guidemaker-webapp:sha-${{ steps.slug.outputs.sha12 }}

```

## Release a version of the software


## Automate deployment to AWS Fargate
Deployment to AWS Fargate can also be automated using workflow and actions. You can use the following actions file to do so. It should work for any regular AWS account; however, will not work for the USDA-AWS account. One of the information/authentications we need to add to the actions file is the account credential for the AWS account, which is fairly constant or does not have to be changed (every 15 min). In the case of USDA-AWS, the credential expires with some (10-15) mins and you need to generate a new token with your username. Thus, deployment via actions is that that smooth. So, rather we have been doing it manually. 

One of the file that noticed in the YAML file is task-definition file, which included information on how to create a cluster in AWS Fargate, as well as information on computing resources. It also has information on AWS docker registry, load-balancer, and auto-scaling. To create a task defination for the first time, you can follow the GUI instruction available at AWS Fargate. Once you have that defined, you can download and save under `.aws` folder. Example of [task file] (https://github.com/USDA-ARS-GBRU/GuideMaker/blob/main/.aws/first-run-task-definition.json) defined for GuideMaker. Then, you can downlod it and save under `.aws` folder. 
    
```
# This workflow will build and push a new container image to Amazon ECR,
# and then will deploy a new task definition to Amazon ECS, on every push
# to the master branch.
#
# To use this workflow, you will need to complete the following set-up steps:
#
# 1. Create an ECR repository to store your images.
#    For example: `aws ecr create-repository --repository-name guidemakerapp --region us-gov-west-1`.
#    Replace the value of `ECR_REPOSITORY` in the workflow below with your repository's name.
#    Replace the value of `aws-region` in the workflow below with your repository's region.
#
# 2. Create an ECS task definition, an ECS cluster, and an ECS service.
#    For example, follow the Getting Started guide on the ECS console:
#      https://us-east-2.console.aws.amazon.com/ecs/home?region=us-east-2#/firstRun
#    Replace the values for `service` and `cluster` in the workflow below with your service and cluster names.
#
# 3. Store your ECS task definition as a JSON file in your repository.
#    The format should follow the output of `aws ecs register-task-definition --generate-cli-skeleton`.
#    Replace the value of `task-definition` in the workflow below with your JSON file's name.
#    Replace the value of `container-name` in the workflow below with the name of the container
#    in the `containerDefinitions` section of the task definition.
#
# 4. Store an IAM user access key in GitHub Actions secrets named `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`.
#    See the documentation for each action used below for the recommended IAM policies for this IAM user,
#    and best practices on handling the access key credentials.

on:
  release:
    branches:
      - main

name: Deploy to Amazon ECS

jobs:
  deploy:
    name: Deploy
    runs-on: ubuntu-latest

    steps:
    - name: Checkout
      uses: actions/checkout@v1

    - name: Configure AWS credentials
      uses: aws-actions/configure-aws-credentials@v1
      with:
        #aws-access-key-id: ${{ secrets.USDA_RAVIN_AWS_ACCESS_KEY_ID }}
        #aws-secret-access-key: ${{ secrets.USDA_RAVIN_AWS_SECRET_ACCESS_KEY }}
        aws-region: us-gov-west-1

    - name: Login to Amazon ECR
      id: login-ecr
      uses: aws-actions/amazon-ecr-login@v1

    - name: Build, tag, and push image to Amazon ECR
      id: build-image
      env:
        ECR_REGISTRY: ${{ steps.login-ecr.outputs.registry }}
        ECR_REPOSITORY: guidemakerapp
        IMAGE_TAG: ${{ github.sha }}
      run: |
        # Build a docker container and
        # push it to ECR so that it can
        # be deployed to ECS.
        # https://stackoverflow.com/questions/61242936/github-action-deploy-docker-to-aws-ecs-ecr
        docker build -f docker-images/webapp/Dockerfile -t $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG .
        docker push $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG
        echo "::set-output name=image::$ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG"
    - name: Fill in the new image ID in the Amazon ECS task definition
      id: task-def
      uses: aws-actions/amazon-ecs-render-task-definition@v1
      with:
        task-definition: .aws/first-run-task-definition.json
        container-name: webapp
        image: ${{ steps.build-image.outputs.image }}

    - name: Deploy Amazon ECS task definition
      uses: aws-actions/amazon-ecs-deploy-task-definition@v1
      with:
        task-definition: ${{ steps.task-def.outputs.task-definition }}
        service: webapp-service
        cluster: mycluster
        wait-for-service-stability: true
```
Broadly, the above actions file builds a docker image, then pushes the built image to the AWS registry, which then is used by AWS Fargate to deploy the web app. Notes: We need to also manually create an AWS registry before creating a task definition file.

Given the auto CD does not work for the USDA-AWS account. We need to use the following steps:
    
**Notes** You need to have an AWS account with USDA, and need to setup `aws-saml-scinet`. More info and how to part for` aws-saml-scinet` is available [here](https://github.com/usda-scinet-tools/aws-saml-scinet)
                     
``` bash
######################## STEPS ########################
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
- How to start bioconda? Here is the [official instructions](https://bioconda.github.io/user/install.html)
- pull request
- Once you sent pull request , make comments at [Bioconda Gitter](https://gitter.im/bioconda/Lobby?utm_source=notification&utm_medium=email&utm_campaign=unread-notifications) so that someone will start looking your code and authorize the branch merge. When you made a pull request, the bioconda server run build and test part. If sucess, then it is ready for review. During the submission of GuideMaker, one error we counter was not matching sha hash code for the released version of software. Given which machine and os, the tools to generate sha code might differ. So, we checked the log generated by bioconda server, then pass the same sha code in the bioconda recipe (YAML) file. This seems to solve the issues. 

[Example of bioconda recipe](https://github.com/USDA-ARS-GBRU/GuideMaker/blob/main/bioconda.recipe/guidemaker/meta.yaml)

## How to develop a web app using streamlit?

```Python
"""guidemaker/app.py A configuration template fo a streamlit web application version of Guidemaker."""

import os
import subprocess
import base64
import pathlib
from pathlib import Path
import uuid
from uuid import uuid4
from contextlib import contextmanager
import shutil

import pandas as pd
import altair as alt
from PIL import Image
import streamlit as st
from streamlit_tags import st_tags_sidebar

import guidemaker



# @contextmanager
# def genome_connect(db_bytes):
#     """Write input genome to local disk and clean after using."""
#     fp = Path(str(uuid4()))
#     with open("input.gbk", 'wb') as fp:
#         for i in db_bytes:
#             if guidemaker.is_gzip(i):
#                 with zip.open(i, 'rt') as f:
#                     fp = bytearray(f)
#                     file.write(fp)
#             else:
#                 with open(i, 'r') as f:
#                     fp = bytearray(f)
#                     file.write(fp)
#     conn = str(fp)
#     try:
#         yield conn
#     finally:
#         pass


@contextmanager
def genome_connect(db_bytes):
    """Write input genome to local disk and clean after using."""
    fp = Path(str(uuid4()))
    with open('input.gbk', 'wb') as file:
        for i in db_bytes:
            fp = bytearray(i)
            file.write(fp)
    conn = str(fp)
    try:
        yield conn
    finally:
        pass

@st.cache(suppress_st_warning=True)
def run_command(args):
    """Run command, transfer stdout/stderr back into Streamlit and manage error."""
    st.info(f"Running:: '{' '.join(args)}'")
    result = subprocess.run(args, capture_output=True, text=True)
    try:
        result.check_returncode()
        # st.info(result.stdout)
        # st.text(result.stderr)
    except subprocess.CalledProcessError as e:
        st.error(result.stderr)
        raise e


def get_binary_file_downloader_html(bin_file, file_label='File'):
    """Binary file downloader in html format."""
    with open(bin_file, 'rb') as f:
        data = f.read()
    bin_str = base64.b64encode(data).decode()
    href = f'<a href="data:application/octet-stream;base64,{bin_str}" download="{os.path.basename(bin_file)}">{file_label}</a>'
    return href


def read_markdown_file(markdown_file):
    """Read markdown file."""
    return Path(markdown_file).read_text()


def guidemakerplot(df):
    """Returns guidemaker plot describing PAM targets."""
    source = df
    brush = alt.selection(type='interval', encodings=['x'])
    binnum = int(round(source['Feature end'].max() / 200, 0))
    display_info = source.columns.tolist()

    # Feature density
    densityF = alt.Chart(source).transform_density(
    'Feature start',
    as_=['Feature start', 'Feature Density'],
    extent=[1, source['Feature end'].max()],
    bandwidth=binnum,
    ).mark_area(color='black', opacity=0.6).encode(
    x=alt.X('Feature start', axis=alt.Axis(title='Genome Coordinates (bp)', tickCount=5)),
    y='Feature Density:Q',
    ).properties(height=50)

    # gRNA density
    densityg = alt.Chart(source).transform_density(
    'Guide start',
    as_=['Guide start', 'Guide Density'],
    extent=[1, source['Feature end'].max()],
    bandwidth=binnum,
    ).mark_area(color='pink', opacity=0.6).encode(
    x=alt.X('Guide start', axis=alt.Axis(title='Genome Coordinates (bp)', tickCount=5)),
    y='Guide Density:Q',
    ).properties(height=50).add_selection(brush)

    # locus tag
    locus = alt.Chart(source).mark_bar(cornerRadiusTopLeft=3, cornerRadiusTopRight=3).encode(
    x='count(locus_tag):Q',
    y=alt.Y('locus_tag', axis=alt.Axis(title='Locus')),
    color='PAM:N',
    tooltip=display_info
    ).transform_filter(
    brush
    ).interactive().properties(height=500)

    gmplot = densityF & densityg & locus
    return gmplot




def main(arglist: list = None):
    """Run web App."""
    header = "GuideMaker"
    subheader = "Software to design CRISPR-Cas guide RNA pools in non-model genomes 🦠 🧬"
    st.markdown(f'<strong style="font-family:Hoefler Text;font-size: 36px;color: #0021A5">{header}</strong>',
                unsafe_allow_html=True)
    st.markdown(
        f'<strong style="font-family:Hoefler Text;font-size: 18px;color: #FA4616">{subheader}</strong>', unsafe_allow_html=True)

    st.markdown("---")

    sessionID = str(uuid.uuid1())
    # st.write(sessionID)
    logfilename = sessionID + "_log.txt"

    # Create a downloads directory within the streamlit static asset directory
    # and write output files to it. Binary downloader as html file  has limited
    # file size for conversion. So, we need to write it to local folder.
    STREAMLIT_STATIC_PATH = pathlib.Path(st.__path__[0]) / 'static'
    DOWNLOADS_PATH = (STREAMLIT_STATIC_PATH / "downloads")
    if not DOWNLOADS_PATH.is_dir():
        DOWNLOADS_PATH.mkdir()

    # Define input parameters and widgets

    multiple_files = st.sidebar.file_uploader("Upload one or more Genome file [ .gbk, .gbk.gz]", type=[".gbk", ".gz"], accept_multiple_files=True)
    genome = list( map(lambda x: x.getvalue(), multiple_files))

    DemoGenome = st.sidebar.selectbox("OR Use Demo GBK",['Carsonella_ruddii.gbk.gz','Pseudomonas_aeruginosa.gbk.gz'])
    demo = open(DemoGenome,"rb")
    #st.write("You selected this option ",xx)

  

    pam = st.sidebar.text_input("Input PAM Motif [ E.g. NGG ] ", "NGG")
    restriction_enzyme_list = st_tags_sidebar(label = 'Restriction Enzymes[e.g. NGRT]:',
                                                                  text  = 'Enter to add more', 
                                                                  value = ['NGRT'])
    #restriction_enzyme_list= st.sidebar.text_input("Restriction Enzymes list [ E.g. NGRT ] ", "NGRT")
    pam_orientation = st.sidebar.selectbox(
        "PAM Orientation [ Options: 3prime, 5prime ]", ("3prime", "5prime"))
    guidelength = st.sidebar.number_input('Guidelength [ Options: 10 - 27 ]', 10, 27, value=20)
    lsr = st.sidebar.number_input('Length of seed region[ Options: 0 - 27 ]', 0, 27, value=10)
    dist = st.sidebar.number_input('Hamming Distance [Options: 0 - 5 ]', 0, 5, value=2)
    before = st.sidebar.number_input('Before [Options: 1 - 500 ]', 1, 500, value=100, step=50)
    into = st.sidebar.number_input('Into [Options: 1 - 500 ]', 1, 500, value=200, step=50)
    knum = st.sidebar.number_input('Similar Guides[Options: 2 - 20 ]', 2, 20, value=3)
    controls = st.sidebar.number_input('Control RNAs', 1, 1000, value=1000, step=100)
    #threads = st.sidebar.number_input('Threads [ Options: 2, 4, 6, 8]', 2, 8, step=2)

    # st.write(genome)
    # st.write(type(genome))
  

    if demo and "input.gbk":
        with genome_connect(demo):
            #st.write("Connection object:", conn)
            args = ["guidemaker",
            "-i", "input.gbk",
            "-p", pam,
            "--guidelength", str(guidelength),
            "--pam_orientation", pam_orientation,
            "--lsr", str(lsr),
            "--dist", str(dist),
            "--outdir", sessionID,
            "--log", logfilename,
            "--into", str(into),
            "--before", str(before),
            "--knum", str(knum),
            "--controls", str(controls),
            "--threads", str(2),
            "--restriction_enzyme_list"]
            scriptorun = args + restriction_enzyme_list
    if genome and "input.gbk":
        with genome_connect(genome):
            #st.write("Connection object:", conn)
            args = ["guidemaker",
            "-i", "input.gbk",
            "-p", pam,
            "--guidelength", str(guidelength),
            "--pam_orientation", pam_orientation,
            "--lsr", str(lsr),
            "--dist", str(dist),
            "--outdir", sessionID,
            "--log", logfilename,
            "--into", str(into),
            "--before", str(before),
            "--knum", str(knum),
            "--controls", str(controls),
            "--threads", str(2),
            "--restriction_enzyme_list"]
            scriptorun = args + restriction_enzyme_list

    if(st.sidebar.button("SUBMIT")):
        # st.markdown("""🏃🏃🏃🏃🏃🏃🏃🏃""")
        run_command(scriptorun)
    

    if os.path.exists(sessionID):

        #source = pd.read_csv(os.path.join("./", sessionID,'targets.csv'))
        source = pd.read_csv(os.path.join("./", sessionID, 'targets.csv'), low_memory=False)

        accession_list = list(set(source['Accession']))
        for accession in accession_list:
            accession_df = source[source["Accession"] == accession]
            accession_info = f"**Accession:** {accession}"
            st.markdown(accession_info)
            st.write(guidemakerplot(accession_df))

        # Targets
        target_tab = "✅ [Target Data](downloads/targets.csv)"
        targets = pd.read_csv(os.path.join("./", sessionID, 'targets.csv'), low_memory=False)
        targets.to_csv(str(DOWNLOADS_PATH / "targets.csv"), index=False)

        # Controls
        control_tab = "✅ [Control Data](downloads/controls.csv)"
        controls = pd.read_csv(os.path.join("./", sessionID, 'controls.csv'), low_memory=False)
        controls.to_csv(str(DOWNLOADS_PATH / "controls.csv"), index=False)

        # logs
        with st.beta_expander("Results"):
            st.write(target_tab)
            st.write(control_tab)
            st.write(get_binary_file_downloader_html(
                logfilename, '✅ Log File'), unsafe_allow_html=True)
    else:
        pass

    # Parameters Dictionary
    image = Image.open(guidemaker.APP_PARAMETER_IMG)
    optionals = st.beta_expander("Parameter Dictionary", False)
    optionals.image(image, caption='GuideMaker Parameters', use_column_width=True)

    with st.beta_expander("Designing Experiments with GuideMaker Results"):
        intro_markdown = read_markdown_file(guidemaker.APP_EXPERIMENT_FILE)
        st.markdown(intro_markdown, unsafe_allow_html=True)

    st.markdown("""
    ##### API documentation 📖

    API documentation for the module can be found [here](https://github.com/USDA-ARS-GBRU/GuideMaker)


    ##### License information ©️

    *Guidemaker was created by the United States Department of Agriculture - Agricultural Research Service (USDA-ARS). As a work of the United States Government this software is available under the CC0 1.0 Universal Public Domain Dedication (CC0 1.0)*

    """)
    

    # Check if the output dir exist, if yes, delete so that previous results are not display
    try:
        shutil.rmtree(sessionID, ignore_errors=True)
        os.remove(logfilename)
        os.remove('input.gbk')
    except FileNotFoundError as e:
        pass

if __name__ == "__main__":
    main()

```
Intersted in the streamlit web app? [Here is the result](https://guidemaker.app.scinet.usda.gov), which is hosted via AWS Fargate.
