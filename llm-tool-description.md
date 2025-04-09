# PII Splicing Arguments

**-File(s) to redact**
	Single File (default), Folder

**-Output File(s)**
	Filename (default), Folder

**-What to redact**
	Default Categories (always on): Last Name, Address Physical and Electronic, Any ID Number 
 
Optional Categories (additional selections): First Name, Organization, Location, Unique Characteristic, Achievement, Event

**-Redaction Model**
   llama 3.2 (default), deepseek R1, GPT-4

**-Redaction Tolerance**
	10000 (default), custom value integer in powers of 10

**-Known Identity**
	Off (default), Description string

**-Verification**
	Off (default), On [Reverse lookup]

**-What to replace with**
	PII Category (Default), Custom Annotation String

**-Output report**
	JSON, HTML full report  

## Definitions
P: number of PII elements in a given text

N: number of non-PII elements in a given text

TP: PII elements correctly identified as PII

FP: non-PII elements incorrectly identifies as PII

TN: non-PII elements correctly identified as non-PII

FN: PII elements incorrectly identified as non-PII

**Sensitivity = TP/P : which should be as close as possible to 1**

**Specificity = TN/N : should be as high as possible but can be sacrificed for higher Sensitivity**

**False Negative Rate (FNR) = FN/P : PII incorrectly identified as non-PII ("PII Miss Rate")**

**False Positive Rate (FPR) = FP/N : Non-PII incorrectly identified as PII ("Overshoot Rate")**

A typical confusion matrix for a given text analyzed looks like this

|   | Predicted PII | Predicted non-PII |
| --- | --- | --- |
|Actual PII | 1.44%  | 0.96% |
|Actual non-PII | 1.44%  | 96.17% |

In more detail, the analysis yields

| Metric | Value | Explanation |
| --- | --- | --- |
| PPV | 50.00% | Correct PII Rate |
| NPV | 99.01% | Correct Non-PII Rate |
| **FPR** | 1.47% | **Overshoot Rate** |
| **FNR** | 40.00% | **PII Miss Rate** |

Some may have differing thresholds for both FNR and FPR. For example, one may prefer a model with a low FNR and they may be okay with a higher FPR.

## Methods

**A.** An LLM is instructed to identify Personal Identifying Information. The PII entity types and their [significance] are: 

1. **Last names**: Full names or Last names [HIGH]

2. **Addresses**: Physical addresses (numbers and street names and ZIP code) [HIGH]

3. **Email Addresses, Social Media Account Names, Usernames, IP Address** [HIGH]

4. **Identification Numbers**: Social Security, Employer Identification, Driver's License, Passport, Vehicle Registration, Membership Numbers, Health Insurance ID [HIGH]

5. **Financial Account Data**: Bank account Numbers, Credit/Debit Card Numbers [HIGH]

6. **Employment and Education information**: Job Title, Employer Name, Employee ID, Academic Institution or School attending/attended, Degree earned, School ID [HIGH]

7. **Birth Date and Location**: Date and Place of Birth [MEDIUM]
   
8. **Medical Information**:  Provider/Hospital Name, Medical History [MEDIUM]

9. **Demographics**: Age, Ethnicity, Race, Religious Affiliation, Non binary Gender Identity [MEDIUM] 

10. **Locations**: Specific locations including cities, towns, landmarks, geographical sites *(mountains, lakes, rivers etc, plains, creeks, beaches)* [MEDIUM]

11. **Organizations**: Officially registered or widely recognized entities, for example *Microsoft, or Boston University* [MEDIUM]

12. **Unique achievements**: Must be specific, verifiable accomplishments, for example *Winning a Medal in a Race, Climbing a Mountain that few do.*  [MEDIUM]

13. **Unique personal traits, features, characteristics**: Must be specific for example *a special tatoo, a birthmark* [LOW] 
                    
14. **Events**: Must be official, named events for example *the 2021 MET Gala, the 2024 Superbowl* [LOW]

The significance (HIGH, MEDIUM, LOW) represents the risk of identifying someone for a given PII information.

-HIGH means that someone can readily be identified by said information.

-MEDIUM means that someone can be identified using said information in combination with other archived information that possibly can be accessed elsewhere (for example public records).  

-LOW means that the information provided alone cannot be used to identify someone, however, in combination with other information in the text and archived information elsewhere, it can significantly increase the identification risk probability to less than 1:10000.   


**B.** Once a list of PII has been identified, and for each element, the engine should interogate if said element could be used to identify someone within a 10000 people pool. For example, *a mention to someone living in New York, NY does not satisfy this condition, whereas a mention to someone living in Stockbridge, MA does satisfy this condition.*


## Specifications

A text redaction engine that can permanently and irreversibly remove personal identifying information (PII) with the following specifications:

-For HIGH significance PII: FNR < 5% (miss ratio)

-For MEDIUM significance PII: FNR < 20% (miss ratio)

-For LOW significance PII: FNR < 50% (miss ratio) 

FNR and FPR to be assessed in a large enough statistical sample (TBD).

## Validation

A benchmarking dataset of texts has been created and annotated for PII. Each PII element is identified by a human reviewer and classified as above.   