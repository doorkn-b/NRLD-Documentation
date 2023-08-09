# Conversion of the National Registry of Large Dams to a shapefile.

Data source- [The National Register of Large Dams - 2019](https://cwc.gov.in/sites/default/files/nrld06042019.pdf)
Pages 41-270 of the registry contains the state-wise distribution with details of each dam. 

## PDF extraction and conversion using SmallPDF.
The required pages were from the registry containing our desired information was isolated (Pages 41-270)
Find the document [here](https://github.com/doorkn-b/Shapefile-for-NRD-Documentation/blob/main/Files/NRLD%20Isolated%20PDF.pdf)

This document with the state-wise list was split state-wise into individual PDFs using the 'Split PDF -> Split 1 PDF into many' tool in SmallPDF.
![](https://github.com/doorkn-b/Shapefile-for-NRD-Documentation/blob/main/Documentation%20Images/splitsmallpdf.png)

This will give us 37 individual PDFs. (29 states and 8 union territories). 

These state-wise PDFs were then converted to Excel using SmallPDF's PDF to Excel converter.
![](https://github.com/doorkn-b/Shapefile-for-NRD-Documentation/blob/main/Documentation%20Images/pdf%20to%20excel.png)

Download and move all the excel files into the folder. 

Go through each excel file and remove any discrepancies created during the conversion to excel. Retain the table only

Add a column stating the dam's State/UT. States with no dams listed can be deleted and ignored. The final table looks something like this 
![](https://github.com/doorkn-b/Shapefile-for-NRD-Documentation/blob/main/Documentation%20Images/Final%20Table.png)

## Python Script to combine all the files.

Once all the excel files have been standardized to have the table only. We will write a python script to combine them into one excel file.



