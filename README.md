# Test4z Sample
The Test4z sample project contains client-side installation of Test4z as well as a series of sample tests that use the Test4z API to run tests on data sets on your z/OS system.

Test4z leverages z/OSMF and Zowe to facilitate batch application testing for flat files on the z/OS platform. Installing Test4z on your z/OS system lets you perform certain operations on your data sets from a client machine. Currently, the following operations are supported:
* **Compare** records contained in two different versions of a data set.
* **Search** through records in a data set.
* Create a **snapshot** of a data set.
* **Update** records in a dataset

## Getting Started
Follow these instructions to set up the Test4z Sample project on your local machine.

### Prerequisites
Before you attempt to install the Test4z Sample project, ensure you meet the following prerequisites:

* Test4z is deployed, configured and running on your z/OS system.
* [Visual Studio Code](https://code.visualstudio.com/download) (VS Code) is installed.
* [Node.js](https://nodejs.org/en/download/) is installed.
* If you use Windows, ensure VS Code uses bash to execute shell (.sh) scripts. Follow these steps:
    1. Download and install [Git BASH](https://git-scm.com/download/win).
    2. In VS Code, select "Git Bash" as your default shell. Check [here](https://code.visualstudio.com/docs/editor/integrated-terminal#_terminal-profiles) for guidance.
* If you have VS Code running, restart it.

### Installing
Once you have met the prerequisites, follow these steps to install the Test4z Sample project on your local machine:

1. Clone the repository to your local machine. In VS Code:
    1. Open the command palette (Ctrl+Shift+P). 
    2. Type and select "Git Clone".
    3. Enter the URL of this repository. 
    4. Use your GitHub access token. ([How to create a personal access token](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token))

2. Open the Test4z Project in VS Code:
    1. Go to File > Open Folder.
    2. Open the cloned git repository.

3. Open a new terminal in VS Code (Terminal > New Terminal) and install the project dependencies using the following command:

        npm install
        
4. Open the zowe.config.json file and enter the following parameters. Ask your mainframe administrator for the specific information.
   1. Add the host property for your LPAR on line 6.
   2. Specify the port of your z/OSMF instance on line 16.
   3. Specify the port of your Test4z service on line 24 and your HLQ on line 27. Adjust other "test4z" properties if needed. 
        * Optional - Test4z is shipped with a self signed certificate. The rejectUnauthorized property is false by default. You may need to set it true depending on your installation. Ask your mainframe administrator for this information. The rejectUnauthorized property is set for all services in this example on line 43.

5. Enter your Mainframe username and password for the LPAR as you are prompted after entering this command:
        npx zowe config secure
                  
6. Open the **src/setup-files/Batch-files/SetupBatchAppDS.sh** file and fill in the required parameters listed at the beginning of the file. **Important:** Make sure you use UPPER CASE for HLQ and Job Card fields.
    
7. Copy the JCL test files to your z/OS system by executing the following command (**make sure all the necessary fields are filled as mentioned in the previous step**):
    
        npm run uploadFiles
    
   The uploadFiles script adds our test data sets, copybooks and batch application to the z/OS. The Test4z sample tests will use this data to perform the tests. We recommend you use the Zowe Explorer to verify this content was copied to your z/OS system.

## Run Test4z Test Samples
After installing the Test4z Sample project and copying the JCL test files to your z/OS system, you can start running the sample tests provided in this project. Follow these steps:

  1. In VS Code, go to the src/test/samples/ directory. 
      
      This folder contains sets of sample tests for each type of Test4z operation.

  2. Open one of the folders. 
  
      Each folder contains a readme file describing an API endpoint that lets you perform one type of Test4z operation. Within each folder there is also a *.test.ts file containing the sample tests.

  3. Open the sample *.test.ts file.

  4. Run the tests. Run the tests using the following command:

          npm run test <FILENAME>.test.ts 
  
      The sample tests use the sample data sets you copied to your z/OS system earlier. If everything was set up properly, all the sample tests should pass.
  ---
 ## The Batch Application used in the Samples
You can search, compare, copy, and update sequential and VSAM files using Test4z. All the services available with Test4z have a sample provided that shows how to use the service. Each sample has also a readme file.

**Note:** Keep in mind that these are just samples and only meant to help you get started with Test4z.

For the samples, a batch application has been created and is submitted using Zowe. The batch application performs the following steps:

1. Filter out records from the main dataset based on the TOTAL_CHECKS, ACTUAL_CHECKS, and PRODUCT_TYPE parameters. The records are filtered according to the following criteria:
((TOTAL_CHECKS=30 **AND** ACTUAL_CHECKS<=3) **OR** (TOTAL_CHECKS=50 **AND** ACTUAL_CHECKS<=5) **OR** (TOTAL_CHECKS=80 **AND** ACTUAL_CHECKS<=8)) **AND** PRODUCT_TYPE IN ('S','C')
2. Modify the filtered records by setting the notification date to the current (today) date.
The modified records are then used by the sample test case.

 ## Additional
*  DB2 samples and DB2 specific readme.md file: [/src/test/samples/db2](./src/test/samples/db2)
 
*  Cascading (dependent job) samples and cascading specific readme.md file: [/src/test/samples/cascade](./src/test/samples/cascade)

 ## Notes
 
 1. During the `npm install`, some of the dependencies may throw errors, as long as `npm install` doesn't interrupt, this errors must be ignored.

 2. The samples using rollbackDataSet method have a limitation for the VSAM datasets. 
 Independently the source dataset volume, as a result, target dataset always has NOVOL 
 (temporary) storage class. If you roll back BackupDataset to OriginalDataset, OriginalDataset 
 becomes a temporary dataset to be deleted. This limitation is in our backlog and will be resolved soon.
 
 3. The compare, search, snapshot, and update samples  generate a temporary data set  when they run.
    We recommend you to delete the temporary data set by opening the terminal and issuing the following command:
    
    `npx zowe zos-files delete data-set 'HLQ.TEST4Z.BATCHAPP.CUSTIN2' -f`
    
    Click [here](https://docs.zowe.org/stable/web_help/index.html?p=zowe_zos-files_delete_data-set)
    for more information about the Zowe data set delete command.

## License
Visit [LICENSE.md](LICENSE.md) file.
