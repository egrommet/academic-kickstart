# install and load tidyverse's readxl package for dealing with xlsx files
install.packages("readxl")
library(readxl)

#load tidyvers
library(tidyverse)

# read in the data - we'll skip some rows to get to headers
lords_4_18 <- read_excel("./data/allowances-expenses-2018-19-month1-april.xlsx", skip = 5)


lords_4_18 <- lords_4_18 %>%
  slice(-1)

spread_lords <- lords_4_18 %>%
  select(Name, `No of Days Attended`, `Location of registered address (county or equivalent)`, Car, 
         `Rail/Ferry/Coach`, Air, Motorbike, `Taxis/Parking/Tolls`) %>%
  mutate(`No of Days Attended` = as.numeric(`No of Days Attended`),
         Car = as.numeric(Car),
         `Rail/Ferry/Coach` = as.numeric(`Rail/Ferry/Coach`),
         Air = as.numeric(Air), 
         Motorbike = as.numeric(Motorbike), 
         `Taxis/Parking/Tolls` = as.numeric(`Taxis/Parking/Tolls`))
  