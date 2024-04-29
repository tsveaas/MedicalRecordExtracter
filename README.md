# MedicalRecordExtracter
Function created to extract patient medical recordings from raw medical text and present them as a list of dictionaries with format  {"parameter":, "value":, "unit":}

ANALYSIS STEPS:

- Load in samples, analyze text structure
- Prioritise new samples as more relevant format
- Load X1.json and analyze structure for parameter abbreviations and synonyms



FUNCTION STEPS:

Cleaning and formatting raw medical text:
- Recordings appear to always be on its own line with only one parameter per line, so will convert raw text to a list of lines
- Make all lowercase to allow string matching 
- Remove unwanted information and then lines that won't have any recordings on. 
	- Remove numbers from within brackets as they just indicate acceptable levels
	- Remove dates
	- Remove lines with no numbers

Formatting X1.json (Parameters file):
- Make all lowercase to allow string matching
- A few repeating parameters with different synonyms lists, have to merge duplicate parameters and their synonyms so they appear once
- Add abbreviations to synonyms to use for detecting parameters in the text

Detecting Params
- Iterate through each line in cleaned medical text and check if there are any parameter synonyms in it
- Use strict matching for shorter synonyms and fuzz.partial matching for longer synonyms to account for slight OCR and recording errors
- If multiple are found in a line choose the longest synonym to account for "iron" getting picked up for "iron Saturation" etc
	- As the longest synonym will likely be the most descriptive
- Make sure word boundaries on either side are there for detected synonyms to account for "t" getting picked up in "transferrin" etc
		
Extracting Units
- split line with detected parameter, on spaces to have a list of the elements
- Find unit element by matching it to common unit formats (/ or %)
- Make sure it is the last matching element as units are stated after values and parameters
- Remove element afterward to not get detected as value as well
	
Extracting Values
- Find elements in the line containing digits (not restricted to only digits to include <, . , etc)
- Make sure it is the last matching element to extract the most recent recording only

Output:
- Create a recording dictionary with keys (param, value, unit) only if all keys have values
- Append all recording dictionaries to list for output
- Only add the recording if the parameter hasn't been recorded already


DETECTED ISSUES:

- Both C02 and HCO3 have the synonym bicarbonate in X1.json (likely shouldn't be a synonym for CO2)


LIMITATIONS / FUTURE CONSIDERATIONS:
 
- Trade-off to consider when matching via fuzz ratio of getting strictly correct information vs more, possibly less accurate information	 
- Perhaps have to consider other unit formattings in addition to only those containing (/ and %) i.e for the unit being time
