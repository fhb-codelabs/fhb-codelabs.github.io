summary: How to create/edit codelabs
id: edit-this-codelabs
categories: codelab
tags: general
status: Published 
authors: Thomas Schuetz

# Creating/changing this codelabs
<!-- ------------------------ -->
## Overview 
Duration: 1

### What You’ll Learn 
- Installing Prerequisites
- Checking out this repository
- Create/update content
- Test your changes
- Updating the codelab

<!-- ------------------------ -->
## Install prerequisites


To compile tutorials, you'll need a working go environment and the claat CLI.

Instructions how to install claat can be found [here](github.com/googlecodelabs/tools/claat).

If you already have got a running go environment, you can install claat using `go get github.com/googlecodelabs/tools/claat`. Ensure that your <GOPATH>/bin directory is in your PATH.

<!-- ------------------------ -->
## Prepare your git environment

### Create a fork of this repository

* Navigate to https://github.com/fhb-codelabs/fhb-codelabs.github.io and click "Fork"
* Select the Account or Organization in which the fork should be created
* Afterwards you should have got a fork of this repository in your account

### Check out your Fork
* Open a shell
* Change to the directory, in which the repository should be cloned
* Clone the repository (replace fhb-codelabs against your username/org).
```
git clone https://github.com/fhb-codelabs/fhb-codelabs.github.io.git
```

<!-- ------------------------ -->
## Create/Update content
* The tutorials can be found in the site/codelabs directory of the repository. 
* To create/update a tutorial, you'll simply have to write in Markdown Syntax. This codelab is named "edit-this-codelabs.md"
* After you finished editing, you need to compile the codelab:
```
cd sites/codelabs
claat export <your-filename>.md
```

* Afterwards, you can test this using gulp

```
cd sites

# Install dependencies if not already done
npm install
gulp serve --codelabs-dir=codelabs
```

* Now a webserver should be started and you can navigate to the server with your browser using http://localhost:8000

* Take a look on your work and proceed to the next step if you like your codelab :-)

<!-- ------------------------ -->
## Create a PR to push your changes to production

* Commit your changes to the github repository

```
git add <filename> or -A
git commit -m "a nice commit message"
git push
```

* Open https://github.com/fhb-codelabs/fhb-codelabs.github.io in your browser

* Click on "Pull requests"

* Create a new Pull Request

* Select the source of your PR, select "master" as base

* Write a nice description and title regarding your changes.

* Wait until your changes get approved

* Afterwards, the tutorial website gets updated

* Thank you for your contribution :-)

