## nCino PPP Test Automation
Test automation repository for the nCino PPP project.

Scope split into two main areas: 

    portal
    middle-offce

### Set up

- Clone the repo
- Import as 'Project form Existing Sources' project in IDE, selecting the build.gradle file (gradle refresh to pull the respective dependencies) 
- Download and save the chromedriver on `<project folder path>\lib\drivers\windows` (if OS is windows)

### Test Execution Setup

#### First Time Setup: Using Run Config
- Right-click on BrowserTest1 and select Run. It will fail. Go to Edit -> Run Configurations for BrowserTest1 and set the following VM arguments 

##### Intellij configuration - Edit Configurations VM Options
    VM Options              :   -Dcukes.env=APOLLOQA
                                -Dcukes.techstack=LOCAL_CH
                                -Dorg.apache.logging.log4j.level=DEBUG
                                -Dcukes.selenium.defaultFindRetries=1
                                -DscreenshotOnFailure=true

for running from CLI in headless Chrome

gradle cukes -Dcukes.env=devtest -Dcukes.techstack=LOCAL_CH -Dorg.apache.logging.log4j.level=DEBUG -Dcukes.selenium.defaultFindRetries=1 -DscreenshotOnFailure=true -Dcukes.testsuite=browsertests -Dchrome.options=--headless;window-size=1920x1080

for running from CLI in non-headless

gradle cukes -Dcukes.env=devtest -Dcukes.techstack=LOCAL_CH -Dorg.apache.logging.log4j.level=DEBUG -Dcukes.selenium.defaultFindRetries=1 -DscreenshotOnFailure=true -Dcukes.testsuite=browsertests

Note: to run the test in another browser update the run config (LOCAL_IE - Internet Explorer, LOCAL_FF - Firefox etc) and copy the respective driver in drivers folder.

#### Using Command Line 
    gradlew cucumberTag -Dcukes.techstack=LOCAL_CH -Dcukes.env=devtest -DscreenshotOnFailure=true -Dcukes.tags=@donothing

#### Test Execution Setup - Concurrent Users
When running a suite of tests, and not just one test at at time, the settings will need to be updated to avoid conccurent user issues.

Go to src/test/resources/config/selenium/runtime.properties and set this flag to true:

    ####Active Users####
    rotateUsers=true

This will use different logins for each thread. Next, check that the active users file for the current environment is all set to no. 

Go to src/test/resources/config/environments/activeUsers-<envName>.properties

    Example for APOLLOQA: activeUsers-APOLLOQA.properties
    
    email1=no
    email2=no
    email3=no
    email4=no
    email5=no

#### Test Execution Setup - Sauce Labs
The framework supports execution in Sauce Labs. Change the VM options to run in Sauce Labs:

##### Intellij configuration - Edit Configurations VM Options
    VM Options              :   -Dcukes.env=APOLLOQA
                                -Dcukes.techstack=SAUCE_SINGLE
                                -Dorg.apache.logging.log4j.level=DEBUG
                                -Dcukes.selenium.defaultFindRetries=1
                                -DscreenshotOnFailure=true
                                -Dcukes.sauceUserName=<enter user name>
                                -Dcukes.sauceAccessKey=16f56a3a-dbfe-49f6-8b53-00512eab36e4
                                -Dcukes.sauceEndPoint=@ondemand.saucelabs.com:443/wd/hub
                                -DBUILD_NUMBER=<give logical name - groups result in Sauce>
                                -Ddataproviderthreadcount=5

You can find the access key on the Sauce Labs under Your Name -> User Settings.

The BUILD_NUMBER parameter will group the results in Sauce Labs, so you can give it a logical name.

#### Test Execution Setup - Local Docker Grid
There is also a docker compose file in the repo for running a local grid.

#####Grid Setup
- See docker compose file in the repo: selenium-grid-compose.yml
- To start the grid: docker-compose -f selenium-grid-compose.yaml up -d
- Launches grid with 3 Chrome nodes. View at: http://localhost:4444/grid/console
- Edit docker compose for different browser or to change the number of nodes

##### Intellij configuration - Edit Configurations VM Options
    VM Options              :   -Dcukes.env=APOLLOQA
                                -Dcukes.techstack=GRID_SINGLE
                                -Dorg.apache.logging.log4j.level=DEBUG
                                -Dcukes.selenium.defaultFindRetries=1
                                -DscreenshotOnFailure=true
                                -Dcukes.seleniumGrid=http://localhost:4444/wd/hub

Navigate to http://localhost:4444/wd/console to see the grid.

#### Test Execution Setup - ApolloProd
To run tests in ApolloProd, update the VM arguments and also check that the active users file for ApolloProd is setup, as described above.

The only change in VM arguments is the environments parameter:

    VM Options              :   -Dcukes.env=APOLLOQA

Additionally, execution is current run from the release/prodRunNew branch since there are data and page object differences in the two environment. Merge changes from develop to this branch prior to kicking off execution.


### Execution Summary Reports
![](documentation/ExecutionSummary.png)
##### HTML Reports
The main output is a rich html report (Allure) with test run statistics, summary and detailed test results with drilldowns, and embedded screenshots.

To view the allure report execute `gradlew allureServe`.

![](documentation/allure1.PNG)

![](documentation/allure2.PNG)

![](documentation/allure3.PNG)

##### Run Summary - Text Report
A supplementary text report (suitable for email) with tabular results.

![](documentation/runSummary.PNG)

