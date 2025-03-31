# R Shiny App with Google BigQuery Integration

## **Project Overview**

This project demonstrates how to create a data-driven **R Shiny application** that integrates with **Google BigQuery**. The application allows users to securely view health data through a login system, with data stored and queried from Google BigQuery. We also covered how to authenticate and interact with Google BigQuery from RStudio using the `bigrquery` package. 

### **File Name**
`README.md`

---

## **What We Accomplished Today**

1. **Shiny App Creation**: 
   - We created an R Shiny app that interacts with a dataset stored in **Google BigQuery**. The app uses a **username and password authentication** system to ensure that only authorized users can view the data.
   
2. **BigQuery Integration**:
   - We set up **Google BigQuery** to store and query data. We used BigQuery's `bigrquery` package to upload data from a CSV file into BigQuery and interact with it from RStudio.

3. **Authentication**:
   - We used **Google Cloud Authentication** through service accounts to securely interact with BigQuery via R. We followed the **OAuth 2.0 authentication process** to securely connect to Google services.
   
4. **Data Upload**:
   - We successfully uploaded mock health data to BigQuery and connected it to our R Shiny app.

---

## **Code Used**

Below are the key pieces of code used to complete the project.

### **Shiny App with Authentication** (UI and Server)

```r
library(shiny)
library(shinyjs)
library(shinymanager)
library(DBI)
library(bigrquery)

# Secure login credentials
credentials <- data.frame(
  user = c("admin"), 
  password = c("admin123"), 
  stringsAsFactors = FALSE
)

# Define UI for the application
ui <- fluidPage(
  useShinyjs(),  # Initialize shinyjs for enhanced UI control
  secure_app(
    fluidPage(
      titlePanel("Patient Health Data"),
      sidebarLayout(
        sidebarPanel(
          h3("Welcome to the Patient Health Data Viewer")
        ),
        mainPanel(
          tableOutput("table")
        )
      )
    )
  )
)

# Define server logic
server <- function(input, output, session) {

  # Authentication check
  res_auth <- secure_server(check_credentials = check_credentials(credentials))

  # Upload Data from BigQuery
  bq_project <- "shiny-app-project-test"
  bq_dataset <- "my_dataset"
  bq_table <- "mock_patient_health_data"
  
  # Setup BigQuery connection
  con <- dbConnect(
    bigrquery::bigquery(),
    project = bq_project,
    dataset = bq_dataset,
    billing = bq_project
  )

  # Query and display the table
  output$table <- renderTable({
    query <- paste("SELECT * FROM `", bq_project, ".", bq_dataset, ".", bq_table, "`", sep = "")
    result <- dbGetQuery(con, query)
    result
  })
}

# Run the application with authentication
shinyApp(ui = secure_ui(ui), server = server)
```

---

### **Uploading Data to Google BigQuery (Using `bigrquery`)**

```r
# Authenticate with service account JSON key
library(bigrquery)
library(DBI)

bq_auth(path = "C:/path/to/service_account_key.json")

# Uploading a DataFrame to BigQuery
project_id <- "shiny-app-project-test"
dataset_id <- "my_dataset"
table_name <- "mock_patient_health_data"

# Prepare the data frame
df <- read.csv("mock_patient_health_data.csv")

# Upload to BigQuery
bq_table <- bq_table(project_id, dataset_id, table_name)
bq_table_upload(bq_table, df, overwrite = TRUE)  # Set overwrite = TRUE if overwriting the table
```

---

## **How to Setup Google BigQuery**

1. **Create a Google Cloud Project**:
   - Go to the [Google Cloud Console](https://console.cloud.google.com/).
   - Create a new project (e.g., `shiny-app-project-test`).

2. **Enable BigQuery API**:
   - In the Google Cloud Console, navigate to **APIs & Services > Library**.
   - Search for **BigQuery API** and enable it for your project.

3. **Create a Dataset**:
   - In BigQuery, create a new dataset (e.g., `my_dataset`) by navigating to **BigQuery** > **Create Dataset**.

4. **Create a Service Account**:
   - Go to **IAM & Admin > Service Accounts** in Google Cloud.
   - Create a new service account, grant it **BigQuery Admin** permissions, and generate a new key (in JSON format).
   - Download the JSON key file (e.g., `service_account_key.json`).

5. **Install Required Packages**:
   - In R, install the necessary libraries to interact with BigQuery:
     ```r
     install.packages("bigrquery")
     install.packages("DBI")
     ```

6. **Authenticate**:
   - Use the `bigrquery::bq_auth()` function to authenticate using the JSON key file:
     ```r
     bq_auth(path = "C:/path/to/service_account_key.json")
     ```

7. **Upload Data**:
   - Use the `bq_table_upload()` function to upload your data to BigQuery.

---

## **Authentication Process (Tidyverse)**

To ensure secure access to your data, we used **OAuth 2.0** for authentication in both BigQuery and the R Shiny app.

- We used the `shinymanager` package to manage authentication within the Shiny app. This package requires users to provide a username and password to access the app.
  
- For BigQuery authentication, we used a **service account** with a JSON key file to ensure secure access to the dataset without exposing sensitive credentials.

---

## **Why It Matters**

This project demonstrates how to build secure, data-driven applications that can interact with cloud databases like Google BigQuery. The ability to use cloud-based data storage (BigQuery) allows for scalable data processing, and integrating it into R Shiny apps opens up possibilities for interactive and real-time data analysis.

This method of securely integrating a database into an application can be used in numerous business cases, from healthcare data analysis to financial reporting. The use of **authentication** ensures that sensitive data remains protected and only accessible to authorized users.

---

## **Conclusion**

By following the steps outlined in this project, we successfully built an R Shiny application that:
- Securely authenticates users,
- Connects to Google BigQuery to retrieve and display data, and
- Provides a smooth workflow for interacting with cloud-based databases.

This project combines key concepts of **cloud data management**, **secure authentication**, and **interactive data visualization**, all of which are essential for building scalable and secure applications.

Feel free to adapt this process for your specific use case or expand it to include more complex features such as data filtering, updates, or real-time queries.

---

### **Further Reading**

- [Google BigQuery Documentation](https://cloud.google.com/bigquery/docs)
- [Shiny Documentation](https://shiny.rstudio.com/)
- [bigrquery R Package Documentation](https://cran.r-project.org/web/packages/bigrquery/bigrquery.pdf)
- [shinymanager Documentation](https://cran.r-project.org/web/packages/shinymanager/shinymanager.pdf)

---

### **License**

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

--- 

This `README.md` file should give your users a comprehensive understanding of the project, including instructions, code snippets, and additional resources for learning. You can save it as `README.md` in your GitHub repository.
