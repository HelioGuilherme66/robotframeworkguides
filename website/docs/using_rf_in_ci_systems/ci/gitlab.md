# GitLab

GitLab is a development platform that provides a Git repository manager providing wiki, issue-tracking and continuous integration and deployment pipeline features, using an open-source license, developed by GitLab Inc.

To set up a CI Pipeline which will run your (test) jobs on a GitLab Runner, you need to create a `.gitlab-ci.yml` file in the root of your repository. This file contains the configuration of your CI Pipeline.

A CI Pipeline is a set of `jobs` that are executed in a specific order.  
`Stages` are used to group `jobs` together and to define the order in which they are executed.

## Example

An example for a CI Pipeline using a Dockerimage which runs Robot Framework jobs:

``` yaml title=".gitlab-ci.yml"
stages:
  - smoke
  - regression

image: marketsquare/robotframework-browser

run-smoke-tests:
  stage: smoke
  script:
    - robot --outputdir reports tests/smoke
  artifacts:
    paths:
      - reports

run-regression-tests:
    stage: regression
    script:
        - robot --outputdir reports tests/regression
    artifacts:
        paths:
            - reports
```

## Stages

``` yaml title=".gitlab-ci.yml"
stages:
  - smoke
  - regression
``` 

The Pipeline has two stages: `smoke` and `regression`.  
The `smoke` stage will be executed first, followed by the `regression` stage.  
Each stage can contain multiple jobs which will be executed in parallel (within the same stage).

## Jobs

``` yaml title=".gitlab-ci.yml"
run-smoke-tests:
  stage: smoke
  ...

run-regression-tests:
  stage: regression
  ...
```

The job `run-smoke-tests` is part of the `smoke` stage.  
The job `run-regression-tests` is part of the `regression` stage.  

Each job can contain multiple `scripts` which will be executed in sequence.  
The scripts are executed in the (Docker)`image` specified in the configuration.

## Image

``` yaml title=".gitlab-ci.yml"
image: marketsquare/robotframework-browser
```

The image is used to run the jobs in the CI Pipeline. It is possible to use a different image for each job. The `image` specified at the top of the configuration is used as the default image for all jobs (unless a job-specific image is specified).  
The default image used here is `marketsquare/robotframework-browser`.

## Scripts

``` yaml title=".gitlab-ci.yml"
script:
  - robot --outputdir reports tests/smoke
```

The `script` is a list of commands that will be executed in sequence for the job.  
The `script` specified here will run the Robot Framework tests in the `tests/smoke` folder and will output the results in the `reports` folder.  
It is possible to use multiple `script` commands in a job.

For example:

``` yaml title=".gitlab-ci.yml"
script:
  - pip install -r requirements.txt
  - mkdir reports
  - robot --outputdir reports tests/smoke
```

## Artifacts

``` yaml title=".gitlab-ci.yml"
artifacts:
  paths:
    - reports
```

The `artifacts` section is used to specify the files and folders that should be stored as artifacts after the job has finished.  
For example, you can store the test results (like the `log.html` and `report.html` files) as artifacts.  
Here, the `reports` folder is stored as an artifact. It contains all the test results and screenshots.  
That's possible, because the `--outputdir` in the `robot` command was set to `reports`.

``` bash
robot --outputdir reports tests/smoke
```  

## Variables

``` yaml title=".gitlab-ci.yml"
variables:
  ROBOT_OPTIONS: "--variable BROWSER:chrome"

run-smoke-tests:
    stage: smoke
    script:
        - robot --outputdir reports $ROBOT_OPTIONS tests/smoke
    artifacts:
        paths:
        - reports
``` 

It is possible to define variables in the configuration, either at the top of the file or in a job.  
Also [predefined GitLab variables](https://docs.gitlab.com/ee/ci/variables/predefined_variables.html) or variables defined for your project settings can be used.  
Especially for handling secrets, it is recommended to use `CI Variables` in your project settings.  

Check out the [GitLab CI Variables](https://docs.gitlab.com/ee/ci/variables/) documentation for more information.  
