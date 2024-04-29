Author Tobias Sveaas

Function created to extract patient medical recordings from raw medical text and present them as a list of dictionarys with format 
{"parameter":, "value":, "unit":}

ANALYSIS STEPS:

- Load in samples, analyse text structure
- Prioritise new samples as more relvant format
- Load X1.json and analyse structure for parameter abbreviations and synonyms



FUNCTION STEPS:

Cleaning and formatting raw medical text:
- Recordings appear to always be on it's own line with only one parameter per line, so will convert raw text to list of lines
- Make all lower case to allow string matching 
- Remove unwanted information and then lines that won't have any recordings on. 
	- Remove numbers from within brackets as they just indicate acceptable levels (had to make sure to account for either ( or [ brackets)
	- Remove dates
	- Remove lines with no numbers

Formatting X1.json (Parameters file):
- Make all lower case to allow string matching
- A few repeating parameters with different synonyms list, have to merge duplicate parameters and their synomyns so they appear once
- Add abbreviations to synonyms to use for detecting parameters in the text

Detecting Params
- Iterate through each line in cleaned medical text and check if there are any parameter synonyms in it
- Use strict matching for shorter synonym and fuzz.partial matching for longer sysnonyms to account for slight OCR and recording erros
- If multiple are found in a line choose the longest synonym to account "iron" getting picked up for "iron Saturation" etc
	- As the longest synonym will likely be the most descriptive
- Make sure word boundarys either side are there for detected synonyms to account for "t" getting picked up in "transferrin" etc
		
Extracting Units
- split line with detected parameter, on spaces to have a list of the elements
- Find unit element by matching it to common unit formats (/ or %)
- Make sure it is the last matching element as units are stated after values and parameters
- Remove element afterwards to not get detected as value aswell
	
Extracting Values
- Find elements in line containing digits (not restricted to only digits to include <, . , etc)
- Make sure it is the last matching element to extract the most recent recording only

Output:
- Create recording dictionary with keys (param, value, unit) only if all keys have values
- Append all recording dictionarys to list for output
- Only add the recording if the parameter hasn't been recorded already


DETECTED ISSUES:

- Both C02 and HCO3 have the synonym bicarbonate in X1 json (likely shouldn't be a sysnonym for CO2)


CONCLUSIONS:
- Created function succesfully extracts raw medical data and classifies them into a list of dictionaries
- Function acheives accuracy values of over 95% when used on samples texts

LIMITATIONS / FUTURE CONSIDERATIONS:
 
- Trade off to consider when matching via fuzz ratio of getting strickly correct information vs more, possibly less accurate information	 
- Perhaps have to consider other unit formattings in addition to only those containing (/ and %) i.e for unit being time
- A couple of recordings picked up from recommendations could be fixed via detecting lines which are recommendations rather than recordings