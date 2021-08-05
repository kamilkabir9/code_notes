# CI/CD

How to apply CI/CD to applications

# Steps
## 1. Configure Build agents.
### Deploy agents to Build Machines.
![alt]( screenShots/2021-08-05_11_59_38.png)
![alt]( screenShots/2021-08-05_12_00_21.png)
![alt]( screenShots/2021-08-05_12_00_57.png)

## 2. Configure Deploy agents.
### Deploy agents to Application Server.
![alt]( screenShots/2021-08-05_12_06_51.png)
![alt]( screenShots/2021-08-05_12_05_23.png)
![alt]( screenShots/2021-08-05_12_06_15.png)

## 3. Configure CI.
Configuring CI helps you build application as soon as changes are checked in (configurable).
### Steps
### 1. Goto to Pipeline(1)
### 2. Goto to Builds(2)
### 3. Click New Pipeline(3)
![Create pipeline]( screenShots/2021-07-13_15_51_27-Builds-Pipelines.png)

### 4. Set correct Server Path
![Set correct Server Path]( screenShots/2021-07-13_16_09_57.png)

### 5. Select ASP.NET template
![alt]( screenShots/2021-07-13_16_15_18.png)

### 6. Select desired Agent Pool and set Artifact Name.
![Agent Pool]( screenShots/2021-07-13_16_17_32.png)

### 7. Set proper Agent Name.
![alt]( screenShots/2021-07-13_16_22_10.png)

### 8. In Build Solution task set MSBuild Arguments as 
```
/p:DeployOnBuild=true /p:PackageAsSingleFile=true /p:_PackageTempDir="$(build.artifactstagingdirectory)" /p:DeployTarget=Package /p:AutoParameterizationWebConfigConnectionStrings=False
```
![alt]( screenShots/2021-07-13_16_25_11.png)

### 9. Add New Agent Job to build Angular project.
![Add agent Job]( screenShots/162937.png)

### 10. Set Proper Agent Job Name
![alt]( screenShots/2021-07-13_16_33_39.png)

### 11. Add a NPM task to the Job. This Task is used to install NPM
![alt]( screenShots/2021-07-13_16_38_29.png)

### 12. Set Correct properties for NPM install Task.
![alt]( screenShots/2021-07-13_16_40_50.png)

### 13. Add a NPM task to the Job. This Task is used to build the angular project.
![alt]( screenShots/2021-07-13_16_42_24.png)

### 14. Set Correct properties for Angular build task.
![alt]( screenShots/2021-07-13_16_45_31.png)

### 15. Add Publish Task to Job
![alt]( screenShots/2021-07-13_16_48_29.png)

### 16. Set Correct Publish Tasks Configuration.
![alt]( screenShots/2021-07-13_16_52_40.png)
![alt]( screenShots/2021-07-13_16_54_24.png)

### 17. Click Save & Queue to build both API and UI
![alt]( screenShots/2021-07-13_16_56_52.png)


## 4. Configure CD
### Continuous Delivery will deploy Build Artifacts from Continuous Build to Application Server using Build Agents.
Go to Piplines > Releases. and click New pipeline.
![alt]( screenShots/2021-08-04_16_42_29.png)
![alt]( screenShots/2021-08-04_16_43_11.png)
![alt]( screenShots/2021-08-04_16_44_22.png)
![alt]( screenShots/2021-08-04_16_45_14.png)
![alt]( screenShots/2021-08-04_16_47_36.png)
![alt]( screenShots/2021-08-04_18_13_23.png)
![alt]( screenShots/2021-08-04_18_20_40.png)
![alt]( screenShots/2021-08-04_18_32_08.png)
![alt]( screenShots/2021-08-04_18_44_09.png)
