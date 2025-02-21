## Connector  Introduction

Connector is a Java based backend extension for WaveMaker applications. Connectors are built as Java modules & exposes java based SDK to interact with the connector implementation.
Each connector is built for a specific purpose and can be integrated with one of the external services. Connectors are imported & used in the WaveMaker application. Each connector runs on its own container thereby providing the ability to have it’s own version of the third party dependencies.

## Features of Connectors

1. Connector is a java based extension which can be integrated with external services and reused in many Wavemaker applications.
1. Each connector can work as an SDK for an external system.
1. Connectors can be imported once in a WaveMaker application and used many times in the applications by creating multiple instances.
1. Connectors are executed in its own container in the WaveMaker application, as a result there are no dependency version conflict issues between connectors.

## About Jasper Report Connector

## Introduction
JasperReports is an open source java reporting engine. JasperReports is a Java class library, and it is meant for those Java developers who need to add reporting capabilities to their applications.

This connector will provides multiple api to generate jasper report.In addition it also support of export following export type
HTML, PDF, XLS, CSV, DOC

## Prerequisite

1. Java 1.8 or above
1. Maven 3.1.0 or above
1. Any java editor such as Eclipse, Intelij..etc
1. Internet connection


## Build
You can build this connector using following command
```
mvn clean install
```

## Deploy
You can import connector dist/jasper.zip artifact in WaveMaker studio application using file upload option.

## Use jasper connector in WaveMaker

### step1
1. Download the latest zip [here](https://github.com/wavemaker/jasper-reports-connector/releases/)
2. Import the downloaded jasper reports Connector zip into your app using the Import Resource option in the Connector folder.  ![image](https://github.com/user-attachments/assets/df95de69-0767-4dab-8ebf-d270daeade6d)
   ![image](https://github.com/user-attachments/assets/e3d74e39-8c23-4081-9cc9-161940f0a113)

### step2: Generating Jasper Report
1. Jasper report can be generated from imported database/ API.
2. Build your report using Jasper Studio, which enables you to build and customize look and feel of the report. Once you design the report export the reportxml(.jrxml) and import into WaveMaker project using Import Resource to the ${ServiceName}/src/ folder
3. Place the .jrxml file in the java service src folder as shown below:
<img width="958" alt="image" src="https://github.com/user-attachments/assets/5507dfbd-9681-48fb-b46f-6847edf04ba6" />
4. The below example shown is for rest API i.e. random user [here](https://randomuser.me/api?results=10&format=json)

### step3: create a java service
1. Create a java service JasperReportsService.
2. Add the following code
 ```
import jakarta.servlet.http.HttpServletRequest;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import com.wavemaker.runtime.security.SecurityService;
import com.wavemaker.runtime.service.annotations.ExposeToClient;
import com.wavemaker.runtime.service.annotations.HideFromClient;
import com.wavemaker.connector.jasper.JasperConnector;
import com.wavemaker.connector.jasper.JasperExportType;
import com.wavemaker.runtime.commons.file.model.DownloadResponse;
import com.wavemaker.runtime.commons.file.model.Downloadable;
import java.io.*;
import java.util.HashMap;
import jakarta.servlet.http.HttpServletResponse;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.web.client.RestTemplate;
   ```

.
   ```
@ExposeToClient
public class JasperReportService {

    private static final Logger logger = LoggerFactory.getLogger(JasperReportService.class);

    @Autowired
    private SecurityService securityService;

    @Autowired
private JasperConnector jasperConnector;

      public Downloadable generatePDFReport() {
            String data = invokeService();
            logger.info("++++++++" +data);
            
            logger.info("Calling connector to generate the jasperRepoet");
            ByteArrayOutputStream pdfReportStream = (ByteArrayOutputStream) jasperConnector.generateReport(JasperExportType.PDF, "example.jrxml", new HashMap<>(), data);
                                
            DownloadResponse downloadableResponse = new DownloadResponse(new ByteArrayInputStream(pdfReportStream.toByteArray()), "application/pdf", "jasper.pdf");

            downloadableResponse.setInline(false);

            return downloadableResponse;
        }
    private String invokeService() {
            String url = "https://randomuser.me/api?results=10&format=json";
            
            RestTemplate restTemplate = new RestTemplate();
            String response = restTemplate.getForObject(url, String.class);
            logger.info(" Rest response : " + response);
            return response;
        }   } 
   ```


### Step 4: In the Main page drag and drop an Iframe onto the canvas.

1. Create a variable for generatePDFReport method in java service
2. Drag and drop the iframe widget and set the Source Property to the filecontents of the variable created as shown below.
<img width="959" alt="image" src="https://github.com/user-attachments/assets/3e474f93-0fea-44d1-81fe-ce4699bd76fc" />
Similar process can be for database api as well.










