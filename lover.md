# LoveR

In this chapter, we carry out the last step of the analysis, the detection of genes with a different expression between the two conditions analyzed. We will offer the user of the application the possibility to visualize the results \(the counting table\) and even to interact dynamically with the results by allowing the user to change the settings of this step \(such as the threshold values chosen for statistical tests or the color of the graphs\). The reproducibility progress will be achieved by saving the settings chosen by the user.

To achieve those objectives:

* Exploring the results
* Simplicity of exploration
* Simple parameters setting
* Reproducibility of the visualization

we will use:

![Logos of R and Shiny application](.gitbook/assets/image%20%28118%29.png)

## The R project

R is a free and participatory project \(many people contribute to the improvement of the R project\). R is known for its advanced functions in statistics and graphics. It can carry out among others: calculations on variables, statistical tests, graphical representations. R can be enriched with many additional libraries, in bioinformatics, data-mining, databases, ... There are possible Interfaces with other languages \(Shell, Python, ...\).

There are many tutorials very well done to take your first steps and learn R. We refer you to these books and prefer to present interesting libraries complementary to R in the context of bioinformatics projects.

### The reference website: CRAN

The CRAN: [https://cran.r-project.org/](https://cran.r-project.org/)

![](.gitbook/assets/image%20%2861%29.png)

### R and its _cheat sheets_

Many cheat sheets are available: [https://www.rstudio.com/resources/cheatsheets/](https://www.rstudio.com/resources/cheatsheets/)

![](.gitbook/assets/image%20%28186%29.png)

### A very large community

![Events and persons to follow](.gitbook/assets/image%20%28155%29.png)

### Rstudio: an IDE at the top!

Rstudio is the Integrated Development Environment \(IDE\) for R. It is free of charge, easy to use, and configurable. The use of an IDE should save a considerable amount of time. [https://www.rstudio.com/](https://www.rstudio.com/)

![The Rstudio logo](.gitbook/assets/image%20%28134%29.png)

The Rstudio IDE is composed of 4 parts: for code edition,for iterative cotrole, for workspace, and for plots.

![The Rstudio IDE](.gitbook/assets/image%20%2829%29.png)

#### How to install RStudio?

We propose you 2 installation ways: from the CRAN or with the docker container.

1. Method CRAN \(order is important!\):
   1. Install R \(from CRAN\): [https://cran.r-project.org/](https://cran.r-project.org/)
   2. Installation of Rstudio
2. Method with docker: [https://www.rocker-project.org/images/](https://www.rocker-project.org/images/)

## Shiny

![The Shiny logo](.gitbook/assets/image%20%2823%29.png)

Shiny is an R ackage that facilitates the creation of interactive web applications directly from R. It is composed of many functions that translate R

```r
h1("A title")
```

 to HTML:

```markup
<h1>A title</h1>
```

Installation of Shiny from R:

```r
install.packages("shiny")
library(shiny)
```

### Why make an application?

* Reproducibility of the visualization
* Reproducibility of the analysis \(creation of a report\)
* Simplify the exploration of results through interaction
* Changing settings without entering the script
* Share our project more easily for non-programmers \(opening our project to the world\)

There are many publications based-on the Shiny package.

![](.gitbook/assets/image%20%28192%29.png)

Here is the number of articles citing Shiny in Pubmed. It is a great success because the Shiny package was delivered first in 2013!

![&quot;Shiny app&quot; search in Pubmed \(years: x-axis, number of articles: y-axis\)](.gitbook/assets/image%20%2819%29.png)

Is Shiny a new language to learn? NO \(if you can speak R\)

And knowledge of HTML, CSS and JS? Not essential but useful for fine design and some interaction cases.

### A web Application

Here is the architecture of a web application. It is composed of two sides, the User Interface \(UI\) and the Server side. Input and Output pass between the two.

![architecture of an application](.gitbook/assets/image%20%2852%29.png)

### Example: creating a small application

There is a documented example in the Bioinfo-fr blog.

![](.gitbook/assets/image%20%28120%29.png)

The article \(in french\) is here: [https://bioinfo-fr.net/rendre-ses-projets-r-plus-accessibles-grace-a-shiny](https://bioinfo-fr.net/rendre-ses-projets-r-plus-accessibles-grace-a-shiny) and the code is on GitHub: [https://github.com/bioinfo-fr/bioinfo-fr\_Shiny](https://github.com/bioinfo-fr/bioinfo-fr_Shiny)

And what about the application of the FAIR\_bioinfo project? It is a more complex application... but not impossible for Shiny beginners! The code is 100% available here: [https://github.com/thomasdenecker/FAIR\_Bioinfo/blob/master/R-code/app.R](https://github.com/thomasdenecker/FAIR_Bioinfo/blob/master/R-code/app.R). There is many similarities between the two applications.

To begin, you obviously need RStudio and Shiny! 

For the example, we will use the "Iris data", that are classical data for many R packages:

* Dataset: IRIS \(measurements on flowers\)
* Source: [https://archive.ics.uci.edu/ml/datasets/iris](https://archive.ics.uci.edu/ml/datasets/iris)

![An Iris flower](.gitbook/assets/image%20%28137%29.png)

The table is composed of 5 columns:

* the length of the sepals
* the width of the sepals
* the length of the petals
* the width of the petals
* the species of flowers

The table is available here: [https://github.com/bioinfo-fr/bioinfo-fr\_Shiny/blob/master/datasetIris.txt](https://github.com/bioinfo-fr/bioinfo-fr_Shiny/blob/master/datasetIris.txt)

The necessary libraries are all available on CRAN:

* Creation of the application: Shiny, Shinydashboard 
* Advanced inputs: shinyWidgets, colorpicker 
* Graphics creation: plotly, ggplot2, googleVis 
* Dynamic table: DT \(DataTable\)

**Bonus**: `anylib` is a function that installs \(if necessary\) and loads libraries. On RStudio code part:

```r
install.packages("anyLib")
anyLib::anyLib(c("shiny", "shinydashboard", "shinyWidgets", "DT", "plotly", "ggplot2", "googleVis", "colourpicker"))
```

To create the application architecture, we use the package `Shinydashboard`:

```r
library(shiny)
library(shinydashboard)
 
ui <- dashboardPage(
  dashboardHeader(),
  dashboardSidebar(),
  dashboardBody()
)
 
server <- function(input, output) { }
 
shinyApp(ui, server)
```

### How to test the application?

Save the code: `app.R` \(and of course version with git!\)

In RStudio, click on the "Run App" button:

![](.gitbook/assets/image%20%28138%29.png)

In a terminal \(in the folder with the app.R file\): 

```r
R -e "shiny::runApp('R-code', port=4444)"
```

The file name is not specified in the command line because app.R is the default name \(like Snakefile, dockerfile, ...\).

The result is:

![](.gitbook/assets/image%20%2893%29.png)

It looks a simple visualization but when compared with the corresponding html code:

```markup
<!DOCTYPE html>
<html>
<head>

  <meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
  <script type="application/shiny-singletons"></script>
  <script type="application/html-dependencies">json2[2014.02.04];jquery[1.12.4];shiny[1.3.0];font-awesome[5.3.1];bootstrap[3.3.7];AdminLTE[2.0.6];shinydashboard[0.7.1]</script>
<script src="shared/json2-min.js"></script>
<script src="shared/jquery.min.js"></script>
<link href="shared/shiny.css" rel="stylesheet" />
<script src="shared/shiny.min.js"></script>
<link href="font-awesome-5.3.1/css/all.min.css" rel="stylesheet" />
<link href="font-awesome-5.3.1/css/v4-shims.min.css" rel="stylesheet" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<link href="shared/bootstrap/css/bootstrap.min.css" rel="stylesheet" />
<script src="shared/bootstrap/js/bootstrap.min.js"></script>
<script src="shared/bootstrap/shim/html5shiv.min.js"></script>
<script src="shared/bootstrap/shim/respond.min.js"></script>
<link href="AdminLTE-2.0.6/AdminLTE.min.css" rel="stylesheet" />
<link href="AdminLTE-2.0.6/_all-skins.min.css" rel="stylesheet" />
<script src="AdminLTE-2.0.6/app.min.js"></script>
<link href="shinydashboard-0.7.1/shinydashboard.css" rel="stylesheet" />
<script src="shinydashboard-0.7.1/shinydashboard.min.js"></script>

</head>

<body class="skin-blue" style="min-height: 611px;">
  <div class="wrapper">
    <header class="main-header">
      <span class="logo"></span>
      <nav class="navbar navbar-static-top" role="navigation">
        <span style="display:none;">
          <i class="fa fa-bars"></i>
        </span>
        <a href="#" class="sidebar-toggle" data-toggle="offcanvas" role="button">
          <span class="sr-only">Toggle navigation</span>
        </a>
        <div class="navbar-custom-menu">
          <ul class="nav navbar-nav"></ul>
        </div>
      </nav>
    </header>
    <aside id="sidebarCollapsed" class="main-sidebar" data-collapsed="false">
      <section id="sidebarItemExpanded" class="sidebar"></section>
    </aside>
    <div class="content-wrapper">
      <section class="content"></section>
    </div>
  </div>
</body>

</html>
```

The different areas of the dashboard are:

![](.gitbook/assets/image%20%28127%29.png)

### Configure our application

#### Adding a title

```r
ui <- dashboardPage(
  dashboardHeader(title = "bioinfo-fr"),
  dashboardSidebar( ),
  dashboardBody( )
)
```

![](https://s3.amazonaws.com/media-p.slid.es/uploads/991142/images/6129446/pasted-from-clipboard.png)

#### Adding a menu

```r
ui <- dashboardPage(
  dashboardHeader(title = "bioinfo-fr"),
  ### Black area:
  dashboardSidebar(
    sidebarMenu(
      menuItem("Lecture des données", tabName = "readData", icon = icon("readme")),
      menuItem("Visualisation des données", tabName = "visualization", icon = icon("poll"))
    )
  ),
  ### Grey area:
  dashboardBody(
    tabItems(
      # Read data
      tabItem(tabName = "readData",
              h1("Lecture des données")
      ),
      
      # visualization
      tabItem(tabName = "visualization",
              h1("Visualisation des données")
      )
    )
  )
)
```

Here, we use icons that are awailable here : [https://fontawesome.com/](https://fontawesome.com/)

The result is:

![](https://s3.amazonaws.com/media-p.slid.es/uploads/991142/images/6129469/pasted-from-clipboard.png)

### Creation of a file reader

Why do we need to create a file reader ? Because we have to configure the read \(column names, separator, quatation marks, ...\) and also, we want to offer the user a preview of the reading.

#### **Import area**

```r
[...]
  dashboardBody(
    tabItems(
      # Read data
      tabItem(tabName = "readData",
              h1("Lecture des données"),
              fileInput("dataFile",label = NULL,
                        buttonLabel = "Browse...",
                        placeholder = "No file selected")
      ),
      
      # visualization
      tabItem(tabName = "visualization",
              h1("Visualisation des données")
      )
    )
  )
[...]
```

**Result**

![](.gitbook/assets/image%20%28215%29.png)

#### Parameters setting area 

3 reading parameters :

* Type of separator 
* Quotation mark type
* Column name \(present or not\)

We use of radio buttons \(5 arguments\) :

* id : identifier
* label : title of the radio button group 
* choices: available choices 
* selected : selected choice
* inline = T : radio button alignment \(T for true\)

```r
[…]
tabItem(tabName = "readData",
              h1("Lecture des données"),
              fileInput("dataFile",label = NULL,
                        buttonLabel = "Browse...",
                        placeholder = "No file selected"),
              
              h3("Parameters"),
              
              # Input: Checkbox if file has header
              radioButtons(id = "header", 
                           label = "Header",
                           choices = c("Yes" = TRUE,
                                       "No" = FALSE),
                           selected = TRUE, inline=T),
              
              # Input: Select separator ----
              radioButtons(id = "sep", 
                           label = "Separator",
                           choices = c(Comma = ",",
                                       Semicolon = ";",
                                       Tab = "\t"),
                           selected = "\t", inline=T),
              
              # Input: Select quotes ----
              radioButtons(id = "quote", 
                           label= "Quote",
                           choices = c(None = "",
                                       "Double Quote" = '"',
                                       "Single Quote" = "'"),
                           selected = "", inline=T)
              
      ),
[…]
```

**Result**

![](.gitbook/assets/image%20%28182%29.png)

#### Preview area

For the preview area we have to work on both sides, the user interface and the server. Reminder of the communication between UI and server sides:

![](.gitbook/assets/image%20%2843%29.png)

Applying of the schema to the preview feature give:

![](.gitbook/assets/image%20%28162%29.png)

To implement this preview feature, we have to compose 4 operations, two on UI side \("File" and "Table display"\) and two on the server side \("File reading" and "Creation of a table display"\).

**UI side**, "File" and "Table display":

```r
tabItems(
      # Read data
      tabItem(tabName = "readData",
              h1("Lecture des données"),
              fileInput("dataFile",label = NULL,
                        buttonLabel = "Browse...",
                        placeholder = "No file selected"),
              
              h3("Parameters"),
              
              [...]

              # Input: Select quotes ----
              radioButtons(inputId = "quote", 
                           label= "Quote",
                           choices = c(None = "",
                                       "Double Quote" = '"',
                                       "Single Quote" = "'"),
                           selected = "", inline=T),

              h3("File preview"),
              dataTableOutput(outputId = "preview")
              
      ),
```

**Server side**

![](.gitbook/assets/image%20%2813%29.png)

**Result**

![](.gitbook/assets/image%20%2833%29.png)

#### Organizing the elements

All the elements we create appear one below the other. We would like to place two of them, "Parameters" and "File preview" next to each other.  The area is pre-cut into 12 columns so to place two elements side by side, simply add the specification of the column number where they are to be positioned. It is done inside a `fluiRow` table invocation:

![](.gitbook/assets/image%20%2827%29.png)

**Result**

![](.gitbook/assets/image%20%2856%29.png)

#### Reading with the settings

We add a button that starts the analysis after reading the file. For the the **UI side**:

```r
[...]
actionButton(inputId = "actBtnVisualisation", label = "Visualisation",icon = icon("play") )
[...]
```

and for the **server side**:

```r
data = reactiveValues()
 
observeEvent(input$actBtnVisualisation, {
    data$table = read.csv(input$dataFile$datapath,
                      header = as.logical(input$header),
                      sep = input$sep,
                      quote = input$quote,
                      nrows=10)
})
```

`reactiveValues` is a special variable that relaunches all the functions that use it in case of changes.

Because reading a long file may take time, we want to send a confirmation message to inform the user that the reading is launched. Code for the **server side**:

```r
observeEvent(input$actBtnVisualisation, {
    data$table = read.csv(input$dataFile$datapath,
                          header = as.logical(input$header),
                          sep = input$sep,
                          quote = input$quote,
                          nrows=10)
    sendSweetAlert(
      session = session,
      title = "Done !",
      text = "Le fichier a bien été lu !",
      type = "success"
    ) 

    updateTabItems(session, "tabs", selected = "visualization")
 
  })
```

There will be two results when the user click on the button: i\) the confirmation message:

![](.gitbook/assets/image%20%2828%29.png)

and ii\) the change of the page after the reading for the "visualization" page.

### Visualization

For the visulization page, we will use the `DT` package: it is very efficient and offers perfect communication with Shiny.

**UI side**

```r
tabItem(tabName = "visualization",
              h1("Visualisation des données"),
              h2("Exploration du tableau"),
              dataTableOutput('dataTable')
      )
```

**Server side**

```r
output$dataTable = DT::renderDataTable(data$table)
```

**Result**

![](.gitbook/assets/image%20%2886%29.png)

### Adding conditional actions

We want to offer user some configurations of personal settings like the columns colors of the background or a view with histogram for numerical values.

```r
output$dataTable = DT::renderDataTable({
    datatable(data$table, filter = 'top') %>% 
      formatStyle('Sepal.Length', 
                  background = styleColorBar(data$table$Sepal.Length, 'lightcoral'),
                  backgroundSize = '100% 90%',
                  backgroundRepeat = 'no-repeat',
                  backgroundPosition = 'center'
      ) %>%
      formatStyle(
        'Sepal.Width',
        backgroundColor = styleInterval(c(3,4), c('white', 'red', "firebrick")),
        color = styleInterval(c(3,4), c('black', 'white', "white"))
      ) %>%
      formatStyle(
        'Petal.Length',
        background = styleColorBar(data$table$Petal.Length, 'lightcoral'),
        backgroundSize = '100% 90%',
        backgroundRepeat = 'no-repeat',
        backgroundPosition = 'center'
      ) %>%
      formatStyle(
        'Petal.Width',
        backgroundColor = styleInterval(c(1,2), c('white', 'red', "firebrick")),
        color = styleInterval(c(1,2), c('black', 'white', "white"))
      ) %>%
      formatStyle(
        'Species',
        backgroundColor = styleEqual(
          unique(data$table$Species), c('lightblue', 'lightgreen', 'lavender')
        )
      )
  })
```

**Result**

![](.gitbook/assets/image%20%28145%29.png)

### Adding graphs

We want now add graphs. We can add static graphics \(a base plot with R or with ggplot2\) or dynamic graphics \(with plotly or google\). We will see here a base plot with parameter setting. 

Adding a graph follows the same two-sided philosophy than other functionalities.

**UI side**

```r
tabItem(tabName = "visualization",
              h1("Visualisation des données"),
              h2("Exploration du tableau"),
              dataTableOutput('dataTable'),
              h2("Graphiques"),
             fluidRow(
                column(3,plotOutput("plotAvecR") )
              )
      )
```

**Server side**

![](.gitbook/assets/image%20%2887%29.png)

**Result**

![](.gitbook/assets/image%20%28140%29.png)

### Interacte with graphic

There are filters associated with the table. These filters support the idea of using only the data selected in the filters for the graphical representation. Here is the code:

```r
output$plotAvecR <- renderPlot({
    if (!is.null(data$table)) {
      plot(data$table$Petal.Length[input$dataTable_rows_all],
           data$table$Sepal.Length[input$dataTable_rows_all], 
           main = "Sepal length vs Petal length (R)",
           ylab = "Sepal length",
           xlab = "Petal length")
    } else {
      NULL
    }
  })
```

We use a test \(`if (!is.null(data$table))`\) to avoid the display if we have not read a table.

![](.gitbook/assets/image%20%28206%29.png)

#### Graphical changes

We offer the user these 4 modifications of the graph:

1. Changing the **color** of the points 
2. Changing the **size** of the points 
3. Changing the **type** of points 
4. Changing the **title** of the graph

A little organization: let's put the graph and the parameters on the same line \(`fluidRow` invocation\):

```r
fluidRow(
    column(4, plotOutput("plotAvecR")),
    column(4, 
        # Code for change 1

        # Code for change 2
    ),
    column(4,         
        # Code for change 3

        # Code for change 4
       )
),
```

Which input for which parameter? We can choose input thank to the gallery of examples associated with their code. Look here: [https://shiny.rstudio.com/gallery/widget-gallery.html](https://shiny.rstudio.com/gallery/widget-gallery.html)

![The widget gallery of examples](.gitbook/assets/image%20%28189%29.png)

We choose:

1. Changing the color of the points: `color picker` \(color box\) 
2. Changing the size of the points: `slider` 
3. Change of point type: `selectinput` 
4. Change of title of th graph: `textinput`

Corresponding code for the **UI side**:

![](.gitbook/assets/image%20%28191%29.png)

**Result**

![](.gitbook/assets/image%20%28107%29.png)

and code for the **server side**:

```text
plot(data$table$Petal.Length[input$dataTable_rows_all],
           data$table$Sepal.Length[input$dataTable_rows_all], 
           main = input$title,
           ylab = "Sepal length",
           xlab = "Petal length",
           pch = as.numeric(input$pch),
           col = input$colR, 
           cex = input$cex)
```

Et voilà!

Even more reproducibility? It would be necessary to add the numbers of the packages used.

### Example of the FAIR\_Bioinfo application

You may look at the complete code in the file `FAIR_app.sh` of the github repository \([https://github.com/thomasdenecker/FAIR\_Bioinfo](https://github.com/thomasdenecker/FAIR_Bioinfo)\). The code is complete but you now have all the keys to look into it.

![](.gitbook/assets/image%20%2870%29.png)

## The R power

In this part we would like to draw your attention to some R packages that we consider useful and mature enough for bioinformatics applications. No application examples will be provided, but you can refer to the documentation for each package.

* To facilitate the exploration of a table: `dplyr` 
* To make more beautiful graphs:`plotly`,`ggplot2`,  ... 
* To read special files

### Table exploration with dplyr

without dplyr:

```text
dataExtract = data[, c("col1", "col5", "col6"]
dataExtract = dataExtract[which(dataExtract$col1 > 10), ]

col = NULL

for(i in dataExtract$col5){
    if(i < 10) {
        col = c(col, "red")
    } else if (i < 15 ) {
        col = c(col, "orange")
    } else if (i < 20 ) {
        col = c(col, "green")
    } else {
      col = c(col, "forestgreen")
    }  
}

dataExtract = cbind(dataExtract , col)
```

with dplyr:

```text
dataExtract = data %>%
    select(col1, col5, col6) %>%
    filter(col1 > 10) %>%
    mutate(col= case_when(col5 < 10 ~ "red",
                          col5 < 15 ~ "orange",
                          col5 < 10 ~ "green",
                          TRUE ~ "forestgreen"))
```

#### Interactive graphic libraries

Using [plotly](https://www.rdocumentation.org/packages/plotly):

![](.gitbook/assets/image%20%2844%29.png)

Using [googleVis](https://developers.google.com/chart/interactive/docs/gallery):

![](.gitbook/assets/image%20%2815%29.png)

### Reading of special files

The focus is on the disciplinary field of bioinformatics. Here are the R libraries and functions to read 3 file formats widely used in bioinformatics. But many other packages exist!

| File format | Library | Function |
| :--- | :--- | :--- |
| fasta | [seqinr](https://www.rdocumentation.org/packages/seqinr) | `read.fasta( )` |
| fastq | [ShortRead](https://www.rdocumentation.org/packages/ShortRead) \(bioconductor\) | `readFastq( )` |
| xlsx | [xlsx](https://www.rdocumentation.org/packages/xlsx) | `read.xlsx( )` |

### Visualize differently

Access to the proportionality of each intersection classes with [UpSetR](https://www.rdocumentation.org/packages/UpSetR):

![](.gitbook/assets/image%20%2878%29.png)

Actualize old matrix visualization with [corrplot package](https://cran.r-project.org/web/packages/corrplot/vignettes/corrplot-intro.html):

![](.gitbook/assets/image%20%2871%29.png)

## Conclusion

To present the principles of the FAIR\_Bioinfo shiny application, we realized with a small application based on other data, the `iris` data often associated with R packages but these data do not always reflect the data to be processed. 

![@rstatsmemes](.gitbook/assets/image%20%2816%29.png)

Thanks to R's strong collaborative development, we are convinced that R is a very rich language. We have presented you with so few packages!

## It's up to you!

Add a page to the shiny application based on `iris` data to specify the version of the packages used.

Change the dataset and add graphs \(for example, a correlation between 2 replicates?\)

