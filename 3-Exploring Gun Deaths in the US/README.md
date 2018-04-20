# Exploring Gun Deaths in the US

The dataset came from FiveThirtyEight. The dataset is stored in the guns.csv file. 
It contains information on gun deaths in the US from 2012 to 2014. Each row in the dataset represents a single fatality. 
The columns contain demographic and other information about the victim.
Each row contains information about the fatality, and the victim. Here's an explanation of each column:

_(blank)_ -- this is an identifier column, which contains the row number. It's common in CSV files to include a unique identifier for each row, but we can ignore it in this analysis.

_year_ -- the year in which the fatality occurred.

_month_ -- the month in which the fatality occurred.

_intent_ -- the intent of the perpetrator of the crime. This can be:
  * Suicide, 
  * Accidental, 
  * NA, 
  * Homicide, 
  * Undetermined.

_police_ -- whether a police officer was involved with the shooting. Either:
  * 0 (false) 
  * 1 (true)

_sex_ -- the gender of the victim. Either: 
  * M 
  * F

_age_ -- the age of the victim.

_race_ -- the race of the victim. Either:
  * Asian/Pacific Islander, 
  * Native American/Native Alaskan, 
  * Black, 
  * Hispanic, 
  * White.

_hispanic_ -- a code indicating the Hispanic origin of the victim.

_place_ -- where the shooting occurred. Has several categories, which you're encouraged to explore on your own.

_education_ -- educational status of the victim. Can be one of the following:
  * 1 -- Less than High School
  * 2 -- Graduated from High School or equivalent
  * 3 -- Some College
  * 4 -- At least graduated from College
  * 5 -- Not available
