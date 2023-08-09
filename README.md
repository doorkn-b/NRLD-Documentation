# Conversion of the National Registry of Large Dams to a shapefile.

Data source- [The National Register of Large Dams - 2019](https://cwc.gov.in/sites/default/files/nrld06042019.pdf)
Pages 41-270 of the registry contains the state-wise distribution with details of each dam. 

## PDF extraction and conversion using SmallPDF.
The required pages (Pages 41-270) containing our desired information was isolated
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
### Script 

Install dependancies 
```
import pandas as pd
import os
```

Specifies the path to a directory where the Excel files are located.
```
folder_path = "C:/Users/arnab/OneDrive/Desktop/Dam Excels"
```

Create a list containing all of the excel files we require. (end with ".xlsx").
```
excel_files = [file for file in os.listdir(folder_path) if file.endswith(".xlsx")]
```

Now we create a loop that starts iterating through each Excel file in the excel_files list.
Construct a 'file_path' with the full path to the current Excel file.
We then use the 'read_excel' function and store the files in a data frame.
Finally, we concatenate the data frames, effectively combining the data from all Excel files. 
The ignore_index=True argument resets the index after concatenation.

```
combined_data = pd.DataFrame()
for file in excel_files:
    file_path = os.path.join(folder_path, file)
    data = pd.read_excel(file_path)
    combined_data = pd.concat([combined_data, data], ignore_index=True)
```

Specify the output path of the combined file and write the combined data to the specified output file.
```
output_file_path ="C:/Users/arnab/combined2.xlsx"

combined_data.to_excel(output_file_path, index=False, engine='xlsxwriter')

print("Combined Excel file saved:", output_file_path)
```

## Modifying the Our Result for GIS.

We now have a combined file with the list of all the dams. Change the serialing accordingly.

The Registry contains a few missing/wrong coordinates for the dams. Even some with a 000 DMT coordinate. Fix these manually.

### Converting the Lat-Longs from Degree-Minute-Second to Floating Point Coordinates for our GIS software to read.
We will do this in excel itself. Make a new excel file and copy the Latitude and Longitude columns into them. We will work in this before transferring it to the original file.

First, the format of the coordinate will be in-- X° Y' Z"-- format. Example- 11° 34' 19".

We will first split these three into seperate cells next to each other to perform our calculations. 

Use these formulas to separate them. Considering A3 is the cell our DMS coordinate is present in.
For X
```
=LEFT(A3, FIND("°", A3) - 1)
```

For Y
```
=MID(A3, FIND("°", A3) + 2, FIND("'", A3) - FIND("°", A3) - 2)
```

For Z
```
=MID(A3, FIND("'", A3) + 2, FIND("""", A3) - FIND("'", A3) - 2)
```

Apply this to all the coordinates. Our sheet should look something like this.

![](https://github.com/doorkn-b/Shapefile-for-NRD-Documentation/blob/main/Documentation%20Images/Split%20into%20cells.png)

Once done, apply the formula that will convert the coordinates to floating point decimals.
```
=B2 + C2/60 + D2/3600
```
Where B2- Degree, C2- Minutes, D2- Seconds.

![](https://github.com/doorkn-b/Shapefile-for-NRD-Documentation/blob/main/Documentation%20Images/FinalCoord.png)

Apply this formula across the entire column. Do it for both latitude and longitude. Copy and paste the values back into our original file.

Save our excel file as a CSV File- (Comma delimited) (*.csv)]

## Import into GIS
We now have our excel file ready to import into our GIS software. I will use QGIS.

Add a Delimited Text Layer
![](https://github.com/doorkn-b/Shapefile-for-NRD-Documentation/blob/main/Documentation%20Images/Add%20Layer.png)

In the dialogue box, specify the file name of our CSV. Under Geometry Definitin, ensure 'Point Coordinates' is selected.

Next to it in the X Field select the column containing Floating Point **Longitude** Values. For the Y Field, the Floating Point **Latitude** Values.

![](https://github.com/doorkn-b/Shapefile-for-NRD-Documentation/blob/main/Documentation%20Images/Dialogue.png)

Make sure the CRS is appropriate (EPSG: 4326 - WGS:84 in this case) and click add.












