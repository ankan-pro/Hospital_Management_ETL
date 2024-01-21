# Hospital_Management_ETL
Hospital Management ETL process
Hospital Management




	Prepared By	
Name	ANKAN GHOSH			


 
Table of Contents
Contents
Table of Contents
Contents
1 Introduction	
1.1Scope of the system
	
2 Approach and Mapping Logic	
2.1 Patient Module
2.1 Physician Module
2.1 Appointment Module
2.1 Insurance Module	
2.1 Billing Module	
3 Terms & Conditions	

 
1.Introduction
In the rapidly evolving landscape of healthcare, effective management of hospital data plays a pivotal role in delivering quality patient care, optimizing operations, and making informed decisions. Extract, Transform, Load (ETL) processes are critical in ensuring that data collected from various sources is cleansed, integrated, and made ready for analysis. This project aims to develop a comprehensive ETL system for hospital management, enhancing the efficiency and accuracy of data-driven decision-making within a healthcare institution.

1.1	Scope of the system
The scope of the system is explained through its modules as follows.
•	Patient Module – This Module stores the details on patient ID, name, Date of birth, gender, address, city, country, phone, and insurance id.
•	Physician Module -- This Module stores the details on Physician ID, Name, Date of joining, specialty, designation and experience.
•	Appointment Module-- This Module maintains the details on Appointment ID, patient ID, physician ID, start date time and examination room.
•	Insurance Module --   This module stores the details on Insurance ID, Insurance Name and Maximum coverage Amount.
•	Billing Module – This module stores the details on Payment ID, Patient ID, Physician ID, Amount to be paid, payment date and Insurance ID.


 
2.  Functional requirements and Mapping Logic
I have taken five flat files (.csv) those are Patient module, Physician Module, Appointment Module, Insurance Module, Billing Module. Then I mapped those documents through a tMap Component. As the Billing_module has all the facts of others table, so I have connected this as main row and other four modules as lookup row.
I have connected all tables with primary key foreign key constrain.
2.1 Patient Module
Functional Requirements 	
1)Produce report on patient details who are not from India, not covered by any insurance.
2)Produce report on patient details above age 50

1)First I have mapped all patients details on the output in tMap. Then in the expression builder I checked Appointment_ID.
Expression: !Patient.Country.equals("INDIA")&&Patient.Insurance_ID ==null

2) First I have mapped all patients details on the output in tMap. . Then in the expression builder I checked if patient details above age 50. 
Expression: (TalendDate.getCurrentDate().getYear()-Patient.Date_of_Birth .getYear())>50

 
2.1 Physician Module
 
Functional Requirements 
1)Report of physician details who are surgeon who joined hospital between 2000 to 2010 (year)
2)To display physician details based on Experience
3)To verify whether the physicians are head of the department 

1)I mapped physician details to the output table and then I used expression builder to fetch the details.
Expression:
Physician.Designation.equals("Surgeon")&& 
TalendDate.compareDate(Physician.Date_of_Joining , TalendDate.parseDate("yyyy-MM-dd", "2000-01-01")) >= 0 && 
TalendDate.compareDate(Physician.Date_of_Joining , TalendDate.parseDate("yyyy-MM-dd", "2010-12-31")) <= 0


2)First, I have mapped the Physician details to the Output table. Then from the tMap map the output to the tSortRow component where I short the columns based on experience. Then store the report using delimited file. 
3)In output table I created a column isHod and use expression logic to check if He is head of the department or not.
Expression: Physician.Designation.equals("Department Head")?"Yes":"No"

2.1 Appointment Module
Functional Requirements 
1)To store patient info who have appointment today.
2)To display patient who have appointment booked in future and covered by insurance.

1)I have mapped all the patients details to the output in tMap. Then in the expression builder I have written the expression for it.
Expression:TalendDate.compareDate(Appointment.Start_Date, TalendDate.getCurrentDate(),"yyyy-MM-dd")== 0.


2)I have mapped all the patient details to the output in tMap then I used expression builder to filter the output.
TalendDate.compareDate(Appointment.Start_Date, TalendDate.getCurrentDate(), "yyyy-MM-dd") > 0 && (Patient.Insurance_ID! =null) 

2.1 Insurance Module
Functional Requirements 
1)To store patient info who have insurance amount greater than 2,00,000 
2)To store and fetch count of patients covered by each insurance.
1)I have created a variable where I handled the null value for insurance id then checked if the insurance amount is greater than 2,00,000
Expression: for variable
Patient.Insurance_ID==null?0:Insurance.Maximum_Coverage_Amount
Then Var.var1>2,00,000
 
2)	I have mapped Insurance name, insurance_ID and Patient_ID to the output of the tMap. There I have created a column Insurance_count.Then I used tAggregate row to group data using Insurance id and name and count patient_ID  for each insurance.
 2.1 Billing Module
Functional Requirements
1)	To store the patient details who don’t have insurance coverage and need to pay the complete amount.
2)	To fetch the patient and the actual amount to be paid (Amount to be paid – Covered by insurance).
3)	To fetch physician who had maximum amount to be paid.
4)	To fetch the list of patients who needs to pay this month.



1)I mapped all patient details to output and in expression builder checked the required filter operation.

2)For this, I have first created a variable to handle the null value for amount.
Expression: Insurance.Maximum_Coverage_Amount == null?0:Insurance.Maximum_Coverage_Amount
Then used this variable in new column as actual amount and used expression to check the scenario.
Expression: Fact.Amount>Var.amt_cvg?(Fact.Amount-Var.amt_cvg):0
 

3)I mapped physician_ID, Name and amount to the output .Then I have used taggregateRow to groupby physician_ID and name.In the operation I used sum function of the Amount.Then I used  tSort row to sort the data descending based on amount and then used tSample row to take the highest one.
4)I have mapped patients name and id then used expression to filter the scenario.
Expression: TalendDate.getPartOfDate("MONTH",Fact.Payment_Date)==TalendDate.getPartOfDate("MONTH",TalendDate.getCurrentDate())&&
TalendDate.getPartOfDate("YEAR",Fact.Payment_Date)==TalendDate.getPartOfDate("YEAR",TalendDate.getCurrentDate())

I mapped all the dimension tables to the output and used tdbSCD component to achieve scd2.

The project Job View:
 


![image](https://github.com/ankan-pro/Hospital_Management_ETL/assets/104769124/475c20e2-6a39-4c0e-993f-56a4361eb3d9)




3. Terms & Conditions

GenCs shall be solely responsible for all its acts and omissions under this program. GenCs will comply at all times with all applicable laws. GenCs shall not use Cognizant’s name, logo and trademark in any promotional materials or other communications with third parties without the prior written consent of Cognizant. Any materials used by GenCs in relation to program will not infringe the copyrights, trademarks, patents, trade secrets or other intellectual property rights, privacy or similar rights of any person or entity. GenCs agrees not to post, draw, make, display any content that is threatening, libelous, obscene, defamatory, abusive, pornographic, or advocates/encourages any conduct that could constitute a criminal offence or give rise to any civil liability. Cognizant its associates’ personal details including but not limited to name, address, contact number shall not be shared or forwarded to any third party, without prior written consent of Cognizant, its associates. All intellectual property provided by Cognizant as part of program shall be owned exclusively by Cognizant. Intern shall indemnify, defend and indemnify Cognizant its associates, officers, directors from and against any claims, demands, loss, damage, liability, causes of action, judgments, or costs and expenses of every nature (including attorney’s fees and expenses) incurred by Cognizant based on any claim that any breach of terms and conditions of this program.

