# Isolation-In-Database-ACID-Transaction

Isolation In ACID Transaction Properties
واحد من أهم المبادئ الموجودة في Database Engines
واللي بتساعد انها تحقق في الداتا consistency
Isolation  ايه هو و ايه المشاكل الي حلها ؟
في المبدأ الأول في     (ACID) (ATOMICITY) 
 الداتا بيز بتتأكد ان الكويريز ياما كلها تتحقق ياما كلها تفشل ودا بيحقق 
CONSISTENCY بشكل مبدأي لو فيه Transaction واحدة بس شغالة 
بس المشكلة بتظهر لو فيه اكتر من  Transaction   شغالة على الداتا بيز في نفس الوقت (Parallel Transactions).
ايه اللي بيحصل لو فيه  Transaction  غيرت في داتا و Transaction  تانية بتستخدمها؟ 
هنا ظهر 4 مشاكل مشهورين واسمهم   Read Phenomena.
1. Dirty Reads
المشكلة دي بتظهر  ده لو اتنين ترانزاكشن شغالين في نفس الوقت وفيه واحدة فيهم عملت تغيير على داتا معينة في الداتا بيز، 
حتى لو معملتش commit الTransaction التانية تقدر تشوف التغيير دا ودي مشكلة كبيرة عشان نفترض أن Transaction 1 عملت تغيير في داتا، وبعدين عملت rollback. 
Transaction 2 هتعمل read للداتا دي قبل ما ال rollback يتم، وبالتالي هتعمل read لقيمة غلط في الداتا بيز. وكدَا وصلنا ان الداتا دي Inconsistent. ومينفعش نعتمد عليها 
2. Non-repeatable Reads
ال Non-repeatable Reads هي انك تعمل read queries  في نفس الTransaction وكل مرة ترجع نتيجة مختلفة. طب دا بيحصل ازاي ؟ هنفترض عندك جدول products 


quantity
price
id
2
10
1


Transaction 1 :
SELECT (price * quantity) FROM products WHERE id = 1
نتيجته هتبقى 30.
في نفس الوقت، Transaction 2 عملت UPDATE على نفس الrow وعملت  commit:
Transaction 2 :
UPDATE products SET quantity = 5 WHERE id = 1
لو كررت نفس الاستعلام تاني في Transaction 1، النتيجة هتتغير وتبقى 50 بدلاً من 30.
ودي مشكلة برده , مع ان فعلا النتيجة الصح 50 لان الابديت علي الquantity حصل فعلا , بس فحالات معينة دا هيعمل مشاكل ان نفس الكويري ترجع نتايج مختلفة 
3. Phantom Reads
ال Phantom Reads مشابهة لـ Non-repeatable Reads، لكن الفرق هنا هو أن Transaction 2 عملت Insert/Delete لصفوف جديدة في نفس الtable الي Transaction 1 شغاله  , عليه ودا برده هيعمل مشكلة ؟
هنفترض برده عندنا نفس الtable 


quantity
price
id
2
10
1


Transaction 1 : 
SELECT SUM(price * quantity) FROM products;
النتيجة هتكون 20.
وفي نفس الوقت، Transaction 2 عملت Insert row جديد فنفس الtable:
Transaction 2 : 
INSERT INTO products(price, quantity) VALUES (20, 2);
لو Transaction 1 كررت نفس الquery النتيجة هتتغير وتبقى 60 ودا لانها دلوقتي بقت بتحسب علي 2 rows فتاني query بعد مكانت بتحسب علي row واحد فاول query وطبعا نفيس المشكلة لو كانت Transaction 2 عملت delete row 
ودا هيعمل مشاكل زي Non-repeatable Reads بالظبط

ببساطة الفرق بين ال Phantom Reads و ال Non-repeatable Reads
Phantom Reads بتحصل بسبب الInsert/Delete queries انما
 الNon-repeatable Reads بتحصل بسبب الUpdate queries


4. Lost Updates رابع مشكلة والاخيرة هي ال 
و دي بتحصل بسبب ال UPDATE Queries وهي ان الابديت بتاع Transaction 1 يعمل overwrite للابديت بتاع Transaction 2 
طب دا بيحصل ازاي ؟
هناخد نفس الproducts table كمثال 


quantity
price
id
2
10
1


Transaction 1 :
SELECT quantity from products where id = 1 

دي نتيجتها هتبقا 2 و في نفس الوقت 
Transaction 2 :
SELECT quantity from products where id = 1 
دي برده هتبقا 2 بس دلوقتي لو Transaction 1 عملت 
update لل quantity 

Transaction 1 :
UPDATE products SET quantity =  quantity + 5 where id = 1 

وفي نفس الوقت Transaction 2 عملت ابديت لل quantity برده

Transaction 2 : 
UPDATE products SET quantity =quantity +  3 where id = 1 

ايه الي هيحصل هنا ؟
لو عملنا بعد منخلص ال 2 transactions   
  SELECT quantity from products where id = 1 هتبقا النتيجة ايه ؟ النتيجة هتبقا 5 وهنا هتظهر المشكلة 
طب الupdate بتاع Transaction 1 راح فين ؟
الي حصل ان الupdate بتاع transaction 2 هيعمل overwrite علي الابديت بتاع transaction 1 !

لانو Transaction 2 مش شايفه Transaction 1 
ومش عارفه ان حصل ابديت تاني علي الfield دا فهي بالنسبالها ال quantity فعلا لسا ب 2 فالبتالي 2 + 3 = 5 فعلا 
طبعا دا بيعمل مشاكل برده فالdata consitenty 
الLost Updates هي اخر Read Phenomena وتعتبر اكتر واحدة حلها مكلف 

ال Read Phenomena بيتحلو بIsolation levels مختلفة علي حسب كل Database Implementation 


فيه 4 Isolation Level مختلفين كل واحد فيهم بيحل عدد مختلف من ال Read Phenomena وهما 


1 - Read Uncommitted
2 - Read Committed
3- Repeatable Read 
4 - Serializable

كل Database بتستخدم Isolation Level مختلف كمثال 
 Postgres بتسخدم ال Read Committed 
Mysql بتسخدم ال  Repeatable Read


طبعا انت تقدر تحدد ال الIsolation Level فكل Transaction علي حسب احتياجك عادي 
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
القرار هنا بيرجعلك علي حسب انت محتاج ايه بالظبط ودا بيفرق طبعا لان فيه 
Tradeoff ما بين ال isolation level والPerformance فاحتياجك وقرار انك تستخدم انهي isolation level هو الي هيفرق 
طبعا كل Isolation Level ليه شرح لوحدو وازاي بيتعمل وايه ال Read Phenomena والي بيحلها بس دا موضوع تاني 

References : 

Fundamentals of Database Engineering By Hussein Nasser

https://medium.com/@yogeshsheoran/read-phenomena-and-transaction-isolation-in-dbms-96171b3dcf62

https://www.geeksforgeeks.org/dbms/transaction-isolation-levels-dbms/


