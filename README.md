# Tuning-random-forest-hyperparameters-with-TidyTuesday-trees-data
The details of the codeset and plots are included in the attached Adobe Acrobat reader (.pdf) file in this repository. 
You need to download the same to view the contents. There are referrals to other contents in BLUE colour also to follow.

A Brief Introduction
=====================

Get the data here

# Get the Data

sf_trees <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-01-28/sf_trees.csv')

# Load via Library("tidytuesdayR")

tuesdata <- tidytuesdayR::tt_load('2020-01-28') 
tuesdata <- tidytuesdayR::tt_load(2020, week = 5)


sf_trees <- tuesdata$sf_trees

Data Dictionary
sf_trees.csv


Cleaning Script
=================

library(tidyverse)
library(here)
library(tidytuesdaymeta)
library(pryr)
library(visdat)
library(skimr)
library(lubridate)
library(leaflet)

create_tidytuesday_folder()

raw_df <- read_csv(here::here("2020", "2020-01-28", "Street_Tree_Map.csv"),
                   col_types = 
                   cols(
                     TreeID = col_double(),
                     qLegalStatus = col_character(),
                     qSpecies = col_character(),
                     qAddress = col_character(),
                     SiteOrder = col_double(),
                     qSiteInfo = col_character(),
                     PlantType = col_character(),
                     qCaretaker = col_character(),
                     qCareAssistant = col_character(),
                     PlantDate = col_character(),
                     DBH = col_double(),
                     PlotSize = col_character(),
                     PermitNotes = col_character(),
                     XCoord = col_double(),
                     YCoord = col_double(),
                     Latitude = col_double(),
                     Longitude = col_double(),
                     Location = col_character()
                   )) %>% 
  janitor::clean_names()

small_df <- raw_df %>% 
  select(-x_coord,-y_coord,-q_care_assistant, -permit_notes) %>% 
  filter(plant_type != "Landscaping") %>% 
  select(-plant_type) %>% 
  separate(plant_date, into = c("date", "time"), sep = " ") %>% 
  mutate(date = parse_date(date, "%m/%d/%Y")) %>% 
  select(-time, -location) %>% 
  arrange(date) %>% 
  rename(legal_status = q_legal_status,
         species = q_species,
         address = q_address,
         site_info = q_site_info,
         caretaker = q_caretaker)

small_df %>% skimr::skim()

small_df %>% 
  write_csv(here::here("2020", "2020-01-28", "sf_trees.csv"))
