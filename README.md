# SQL-Exercise-Zoho

**Problem**: We conduct Olympiad exams for our partner schools every year to evaluate the standard of the students. Here we have provided you with the sample data for the exam. To get proper insight into the exam you need all the 5 tables, data for all those 5 tables are given in the link; you can import the data directly inside the zoho analytics and start working on. Datasets are in csv files. </br>

➢ Table 1: student_list – List of students who attended the Olympiad exam from Learn Basics Public School. </br>
➢ Table 2:school_class – Since we are dealing with different class and section of a school we are maintaining section_id for each class and section. </br>
➢ Table 3: student_response – The Learn Basics Olympiad is an objective exam, student response for every question was recorded in this table. </br>
a. 5 options (‘A’, ‘B’, ‘C’, ‘D’ and ‘E’) are provided for each question. </br>
b. Out of 5 options only ‘A’, ‘B’, ‘C’ and ‘D’ are the valid options, students can pick ‘E’ option when they think they haven’t learnt the concept yet. </br>
➢ Table 4: correct_answers – This table has the correct answer for all the questions in math and science. </br>
➢ Table 5: question_paper_code – Since we are dealing with 3 classes and 2 subjects, we are maintaining a separate question paper code for each class and each subject. </br>

**Required output of Task 1**: </br>
Now, the objective is to validate the student response and present it in a single table with list of columns mentioned below using SQL. Create this table using query table in Zoho Analytics. </br>

**Required output table with column data**: </br>

![Screenshot 2025-05-19 222639](https://github.com/user-attachments/assets/64c394a5-42bb-43fa-a72b-f2c2aa48b020) </br>

**Required Technology**: Zoho Analytics, Knowledege on SQL </br>
**Link**: https://analytics.zoho.in/open-view/412231000000002326/1f93e4d05d1e6e3bd4ec15dbffa7f029 </br>
**Output table**: Available on PDF </br>

**Query code**: </br>
```sql SELECT
		 sl."roll_number",
		 sl."student_name",
		 sc."class",
		 sc."section",
		 sc."school_name",
		 /* Math Metrics*/ COALESCE(SUM(CASE
				 WHEN sr."option_marked"  = ca."correct_option"
				 AND	qpc."subject"  = 'Math' THEN 1
				 ELSE 0
			 END), 0) AS math_correct,
		 COALESCE(SUM(CASE
				 WHEN sr."option_marked"  != ca."correct_option"
				 AND	sr."option_marked"  IN ( 'A'  , 'B'  , 'C'  , 'D'  )
				 AND	qpc."subject"  = 'Math' THEN 1
				 ELSE 0
			 END), 0) AS math_wrong,
		 COALESCE(SUM(CASE
				 WHEN sr."option_marked"  = 'E'
				 AND	qpc.subject  = 'Math' THEN 1
				 ELSE 0
			 END), 0) AS math_yet_to_learn,
		 (COALESCE(SUM(CASE
				 WHEN sr."option_marked"  = ca."correct_option"
				 AND	qpc."subject"  = 'Math' THEN 1
				 ELSE 0
			 END), 0) * 10) AS math_score,
		 ROUND(COALESCE((SUM(CASE
				 WHEN sr."option_marked"  = ca."correct_option"
				 AND	qpc."subject"  = 'Math' THEN 1
				 ELSE 0
			 END) * 100.0) / NULLIF(COUNT(DISTINCT CASE
				 WHEN qpc."subject"  = 'Math' THEN sr."question_number"
			 END), 0), 0), 2) AS math_percentage,
		 /* Science Metrics*/ COALESCE(SUM(CASE
				 WHEN sr."option_marked"  = ca."correct_option"
				 AND	qpc."subject"  = 'Science' THEN 1
				 ELSE 0
			 END), 0) AS science_correct,
		 COALESCE(SUM(CASE
				 WHEN sr."option_marked"  != ca."correct_option"
				 AND	sr."option_marked"  IN ( 'A'  , 'B'  , 'C'  , 'D'  )
				 AND	qpc."subject"  = 'Science' THEN 1
				 ELSE 0
			 END), 0) AS science_wrong,
		 COALESCE(SUM(CASE
				 WHEN sr."option_marked"  = 'E'
				 AND	qpc.subject  = 'Science' THEN 1
				 ELSE 0
			 END), 0) AS science_yet_to_learn,
		 (COALESCE(SUM(CASE
				 WHEN sr."option_marked"  = ca."correct_option"
				 AND	qpc."subject"  = 'Science' THEN 1
				 ELSE 0
			 END), 0) * 10) AS science_score,
		 ROUND(COALESCE((SUM(CASE
				 WHEN sr."option_marked"  = ca."correct_option"
				 AND	qpc."subject"  = 'Science' THEN 1
				 ELSE 0
			 END) * 100.0) / NULLIF(COUNT(DISTINCT CASE
				 WHEN qpc."subject"  = 'Science' THEN sr."question_number"
			 END), 0), 0), 2) AS science_percentage
FROM  "student_response" sr
JOIN "student_list" sl ON sr."roll_number"  = sl."roll_number" 
JOIN "school_class" sc ON sl."section_id"  = sc."section_id" 
JOIN "correct_answers" ca ON sr."question_paper_code"  = ca."question_paper_code"
	 AND	sr."question_number"  = ca."question_number" 
JOIN "question_paper_code" qpc ON sr."question_paper_code"  = qpc."paper_code"  
GROUP BY sl."roll_number",
	 sl."student_name",
	 sc."class",
	 sc."section",
	  sc."school_name" 

   
