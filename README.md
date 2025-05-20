### **README File for AMC Study**

---

## **1. System Requirements**

### **Software Dependencies:**
- **R (version 4.4.1 or higher)** – The software is developed and tested using the R programming language.
- **Required R Packages**:
  - `tidyverse` (A Grammar of Data Manipulation)
  - `readxl` (Read Excel Files)
  - `INLA` (Full Bayesian Analysis of Latent Gaussian Models using Integrated Nested Laplace)
  - `patchwork` (The Composer of Plots)
  - `ggprism` (A 'ggplot2' Extension Inspired by 'GraphPad Prism')

### **Operating Systems:**
- **Windows** (tested on `x86_64-w64-mingw32` platform)
- **Linux (Ubuntu/Debian)**
- **macOS (tested on version 10.15 or higher)**

### **Non-Standard Hardware:**
- **No specific hardware requirements**. Standard desktop computers with 4GB of RAM or more should be sufficient.

### **Software Versions Tested:**
- R version 4.4.1 (Pile of Leaves) – tested and compatible.
- All required libraries are compatible with R version 2 and above.

---

## **2. Installation Guide**

### **Instructions:**

1. **Install R**:  
   Download and install R from the official R website: [https://cran.r-project.org/](https://cran.r-project.org/).
   
2. **Download RStudio**:  
   RStudio is a popular integrated development environment (IDE) for R, making it easier to write and debug R scripts. Below are the steps to install RStudio:
  
   Download RStudio:
   Visit the official RStudio website at https://posit.co/download/rstudio-desktop/. Select the version suitable for your operating system (Windows, macOS, or Linux).
  
   Install RStudio:
  
   Windows: Run the downloaded .exe file and follow the installation wizard. Default settings are typically sufficient.
   macOS: Open the downloaded .dmg file, drag the RStudio icon into the Applications folder, and follow any additional prompts.
   Linux: Install the .deb or .rpm package depending on your distribution using the package manager, e.g., for Ubuntu:
   ```bash
   sudo apt install ./rstudio-<version>.deb
   ```
   Verify Installation:
   Open RStudio and ensure it detects the installed R version automatically. If not, ensure the R binary path is correctly set in RStudio preferences.

3. **Install Required R Packages**:  
   Open R or RStudio and install the required packages by running the following command:
   ```r
   install.packages(c("tidyverse", "readxl", "pactchwork", "ggprism"))
   ```
   For INLA package:
   ```r
   if (!requireNamespace("BiocManager", quietly = TRUE))
   install.packages("BiocManager")
   BiocManager::install("Rgraphviz")
   install.packages("INLA", repos=c(getOption("repos"), INLA="https://inla.r-inla-download.org/R/stable"), dep=TRUE)
   ```

### **Typical Install Time**:
On a normal desktop computer: The process typically takes 5–10 minutes, depending on internet speed and system performance.
By completing this step, you will have a fully functional R and RStudio environment for running the provided code.

---

### **3. Demo**

#### **Instructions to Run on Data:**

To run the demo on the provided data, follow these steps:

1. **Download and Extract Files**:  
   Ensure that all four folders and their corresponding files are placed within the same directory.

2. **Open RStudio**:  
   Launch RStudio and navigate to the folder where the files are stored.

3. **Load and Install Dependencies**:  
   Open the relevant script file from each folder and run the initial part of the script to install required packages:
   ```r
   install.packages(c("tidyverse", "readxl", "patchwork", "ggprism"))
   library(tidyverse)
   library(readxl)
   library(patchwork)
   library(ggprism)
   ```
   This will install and load the necessary libraries to run the code.

4. **Load Data**:  
   After the dependencies are installed, run the script to load the dataset:
   ```r
   AMC_all <- read_xlsx("Weibin_SourceData_Fig1.xlsx", sheet = 1)
   AMC_region <- read_xlsx("Weibin_SourceData_Fig1.xlsx", sheet = 2)
   AMC_level <- read_xlsx("Weibin_SourceData_Fig1.xlsx", sheet = 3)
   ```

5. **Process and Aggregate Data**:  
   Run the section of the code to process and aggregate the data, as specified in the `Background` part:
   ```r
    Background <-
      AMC_all %>% 
      mutate(Q1 = Quarter == "Q1") %>% 
      ggplot(aes(x = ym(TimeID), y = `DDDs per 100 patient-days`)) +
      annotate(
        "rect", alpha = 0.5, fill = "gray70",
        xmin = ym(202001), xmax = ym(202301),
        ymin = -Inf, ymax = Inf) +
      annotate(
        "text", label = "COVID-19\npandemic",
        x = ym(202107), y = Inf, color = "black", 
        family = "serif", lineheight = 0.7, 
        hjust = 0.5, vjust = 1.2) +
      scale_x_date(
        expand = expansion(c(0.02, 0.02)),
        date_labels = "%Y", 
        breaks = seq(ym(201303), ym(202409), "1 year"),
        date_minor_breaks = "3 months") +
      labs(x = "", y = "") +
      theme(
        legend.background = element_rect(colour = "black"),
        legend.title = element_text(size = 13),
        legend.text = element_text(size = 12),
        strip.background = element_rect(colour = "grey85", fill = "grey85"),
        panel.background = element_rect(fill = "grey95"),
        axis.ticks.length.x = unit(0.15, "cm"),
        axis.title = element_text(size = 12),
        text = element_text(family = 'serif')) +
      guides(
        x = guide_prism_minor(),
        y = guide_prism_minor())
   ```

7. **Visualize Data**:  
   Finally, use the `Visualization` section to generate plots. Run the following:
   ```r
   # facets
    plot_all <-
      Background + 
      labs(y = "Antibiotic use intensity (DDDs per 100 patient-days)") +
      geom_line() +
      geom_point(
        aes(group = Q1, colour = Q1), size = 3) +
      scale_y_continuous(
        breaks = seq(0, 50, 2),
        expand = c(0.2, 0.2)) +
      scale_color_manual(
        name = "Quarter",
        breaks = c(T, F),
        values = c("#D7191C", "grey20"),
        labels = c("Q1", "Q2/Q3/Q4")) +
      theme(
        legend.position = "inside",
        legend.position.inside = c(0.85, 0.85))
    
    plot_region <-
      Background %+% AMC_region +
      geom_point(
        aes(colour = zone), key_glyph = "timeseries") +
      geom_line(
        aes(colour = zone), key_glyph = "timeseries") +
      scale_colour_manual(
        name = "Region",
        values = c("#040676", "#397FC7", "#F1B656")) +
      theme(
        legend.position = "inside",
        legend.position.inside = c(0.92, 0.85))
    
    plot_level <-
      Background %+% AMC_level +
      geom_point(
        aes(colour = HosLevel), key_glyph = "timeseries", 
        stat = "summary", fun = "median") +
      geom_line(
        aes(colour = HosLevel), key_glyph = "timeseries", 
        stat = "summary", fun = "median") +
      scale_colour_manual(
        name = "Hospital level", 
        values = c("#C3B0D2", "#D4242A")) +
      theme(
        legend.position = "inside",
        legend.position.inside = c(0.85, 0.75))
    
    # patchwork
    ggsave(
      "Weibin_Fig1.png",
      width = 12, height = 8, dpi = 300, 
      plot = 
        plot_all + (plot_region / plot_level) +
        plot_layout(widths = c(1.2, 1)) +
        plot_annotation(tag_levels = 'A') & 
        theme(
          axis.text.x = element_text(hjust = 0.2, size = 10),
          axis.text.y = element_text(size = 12),
          axis.title = element_text(size = 16),
          panel.grid.minor.x = element_blank(),
          axis.line = element_line(),
          plot.margin = margin(r = 10),
          plot.tag = element_text(size = 25, face = "bold")))
   ```
   This will produce a plot that matches the `Weibin_Fig1.png` file in the `01 Overall intensity` folder.

#### **Expected Output:**

After running all the scripts, you should see the following outputs:
- **Data Processing Output**: The dataset will be processed and aggregated as required for the analysis.
- **Visualization Output**: A plot (`Weibin_Fig1.png`) will be generated that visualizes the processed AMR data. The plot will show the trends and patterns in AMR data over time, consistent with the figure provided in the folder.

![Weibin_Fig1](https://github.com/user-attachments/assets/7e82be7d-f32d-4896-9ec4-314aa36a359f)


#### **Expected Run Time on a "Normal" Desktop Computer:**

- **Installation of dependencies**: Approximately 2-3 minutes (depending on internet speed).
- **Data Processing and Analysis**: The data processing, model fitting, and visualization steps will take approximately **Half a minute to a minute** on a typical desktop computer with 4GB of RAM or more. Complex models or larger datasets might take slightly longer.

---

### **4. Instructions for Use**

#### **How to Run the Software on Your Data:**

To run the software on your own data, follow these general steps:

1. **Prepare Your Data**:  
   Ensure your data is formatted as `.xlsx` files, similar to the example datasets provided in the folders. Place your data files in the appropriate folder based on the type of analysis you wish to conduct.

2. **Load and Install Dependencies**:  
   As shown in the demo, install the required R packages.

3. **Run Your Code**:  
   Open and run the code files in the appropriate order.

4. **Check the Output**:  
   After running the scripts, check the output directory for your visualizations and processed data.

#### **Reproduction Instructions**:

To reproduce the results presented in the article, you should follow these instructions:

1. **Ensure your environment matches the required software versions** (R version 4.4.1 and required packages).
2. **Use the provided datasets** or format your own data to match the structure in the example files.
3. **Run all scripts in the order provided** for each section, ensuring that each step is completed before moving to the next.
4. **Compare the output**: After running the scripts, compare the generated outputs (e.g., plots) with the ones in the article to verify consistency.

By following these steps, you can reproduce the analysis and results described in the article using your own data or the provided datasets.

---
