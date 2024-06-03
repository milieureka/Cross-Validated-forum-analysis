# Cross Validated - Stack Exchange text mining and sentiment analysis

## Language:
Python: numpy, pandas, NLP (SentimentIntensityAnalyzer)

## Overview:

I found text analysis particularly compelling due to its ability to render data in a more personal and nuanced manner. This personalization arises from how text data inherently captures individual sentiments, expressions, and linguistic nuances, offering profound insights into human behaviors and societal trends. I also particulary interest to see the sentiment and demographic of user on some hot topic in a big forum, that why I target Cross Validated in Stack Exchange

**1. Starting off:**
- Choose any StackExchange site dealing with topics that you find interesting: https://stackexchange.com/sites?view=list#traffic.
- Download the site’s most recent datadump from: https://archive.org/details/stackexchange/
- Read the description of all the data tables published at: https://meta.stackexchange.com/questions/2677/.

**2. DataFrame construction:**
Since the download file will be in xml format, you can directly use Python's built in library, ElementTree, that has functions to read and manipulate XMLs. However, for me I more familar with normal interaction with csv and pandas so I used ElementTree to convert to csv and frame to dataframe.

Buid a function to convert XML data into a CSV format:

- Parsing XML by input_file using ET.parse, and it retrieves the root of the XML tree.
```
def xml_to_csv(input_file, output_file):
    tree = ET.parse(input_file)
    root = tree.getroot()
    all_keys = set()
```
- Extracting Headers: iterates over all elements tagged as <row> under the root, collecting all unique attribute names from these elements. These names are used as column headers in the resulting CSV file.
```
for element in root.findall('.//row'):
      all_keys.update(element.attrib.keys())

    header = list(all_keys)
```
- Writing to CSV: opens a new CSV file specified by output_file for writing. It first writes the headers. Then, for each <row> element found in the XML, it creates a list where each entry corresponds to the value of the XML attribute for each header (column). If an attribute is missing from an element, it fills that position with an empty string. This list forms a row in the CSV file, which is then written to the file.
```
with open(output_file, 'w',newline='', encoding='utf-8') as csv_file:
      writer = csv.writer(csv_file)
      writer.writerow(header)
      for element in root.findall('.//row'):
        row = [element.attrib.get(key,'') for key in header]
        writer.writerow(row)
```
- Finaly, call the function for multiple files processing.

**3. Weighted Sentiment Plot**

Uses SentimentIntensityAnalyzer from VADER to compute the compound sentiment score (number of upvote) of the comment text. The compound score ranges from -1 (very negative) to 1 (very positive).


