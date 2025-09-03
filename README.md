<!-----



Conversion time: 0.578 seconds.


Using this Markdown file:

1. Paste this output into your source file.
2. See the notes and action items below regarding this conversion run.
3. Check the rendered output (headings, lists, code blocks, tables) for proper
   formatting and use a linkchecker before you publish this page.

Conversion notes:

* Docs to Markdown version 1.0β44
* Wed Sep 03 2025 15:36:25 GMT-0700 (PDT)
* Source doc: Isolation In ACID Transaction Properties
* This is a partial selection. Check to make sure intra-doc links work.
* Tables are currently converted to HTML tables.
----->


<p dir="rtl">
Isolation In ACID Transaction Properties</p>


<p dir="rtl">
واحد من أهم المبادئ الموجودة في Database Engines</p>


<p dir="rtl">
واللي بتساعد انها تحقق في الداتا consistency</p>


<p dir="rtl">
Isolation  ايه هو و ايه المشاكل الي حلها ؟</p>


<p dir="rtl">
في المبدأ الأول في     (ACID) (ATOMICITY)  \
 الداتا بيز بتتأكد ان الكويريز ياما كلها تتحقق ياما كلها تفشل ودا بيحقق </p>


<p dir="rtl">
CONSISTENCY بشكل مبدأي لو فيه Transaction واحدة بس شغالة </p>


<p dir="rtl">
بس المشكلة بتظهر لو فيه اكتر من  Transaction   شغالة على الداتا بيز في نفس الوقت (Parallel Transactions). \
ايه اللي بيحصل لو فيه  Transaction  غيرت في داتا و Transaction  تانية بتستخدمها؟ </p>


<p dir="rtl">
هنا ظهر 4 مشاكل مشهورين واسمهم   <strong>Read Phenomena</strong>.</p>


1. Dirty Reads

<p dir="rtl">
المشكلة دي بتظهر  ده لو اتنين ترانزاكشن شغالين في نفس الوقت وفيه واحدة فيهم عملت تغيير على داتا معينة في الداتا بيز، </p>


<p dir="rtl">
حتى لو معملتش commit الTransaction التانية تقدر تشوف التغيير دا ودي مشكلة كبيرة عشان نفترض أن Transaction 1 عملت تغيير في داتا، وبعدين عملت rollback. </p>


<p dir="rtl">
Transaction 2 هتعمل read للداتا دي قبل ما ال rollback يتم، وبالتالي هتعمل read لقيمة غلط في الداتا بيز. وكدَا وصلنا ان الداتا دي Inconsistent. ومينفعش نعتمد عليها </p>


2. Non-repeatable Reads

<p dir="rtl">
ال Non-repeatable Reads هي انك تعمل read queries  في نفس الTransaction وكل مرة ترجع نتيجة مختلفة. طب دا بيحصل ازاي ؟ هنفترض عندك جدول products  \
</p>



<table>
  <tr>
   <td>quantity
   </td>
   <td>price
   </td>
   <td>id
   </td>
  </tr>
  <tr>
   <td><p dir="rtl">
2</p>

   </td>
   <td><p dir="rtl">
10</p>

   </td>
   <td><p dir="rtl">
1</p>

   </td>
  </tr>
</table>


Transaction 1 :

SELECT (price * quantity) FROM products WHERE id = 1

<p dir="rtl">
نتيجته هتبقى 30.</p>


<p dir="rtl">
في نفس الوقت، Transaction 2 عملت UPDATE على نفس الrow وعملت  commit:</p>


Transaction 2 :

UPDATE products SET quantity = 5 WHERE id = 1

<p dir="rtl">
لو كررت نفس الاستعلام تاني في Transaction 1، النتيجة هتتغير وتبقى 50 بدلاً من 30. \
ودي مشكلة برده , مع ان فعلا النتيجة الصح 50 لان الابديت علي الquantity حصل فعلا , بس فحالات معينة دا هيعمل مشاكل ان نفس الكويري ترجع نتايج مختلفة </p>


3. Phantom Reads

<p dir="rtl">
ال Phantom Reads مشابهة لـ Non-repeatable Reads، لكن الفرق هنا هو أن Transaction 2 عملت Insert/Delete لصفوف جديدة في نفس الtable الي Transaction 1 شغاله  , عليه ودا برده هيعمل مشكلة ؟</p>


<p dir="rtl">
هنفترض برده عندنا نفس الtable  \
</p>



<table>
  <tr>
   <td>quantity
   </td>
   <td>price
   </td>
   <td>id
   </td>
  </tr>
  <tr>
   <td><p dir="rtl">
2</p>

   </td>
   <td><p dir="rtl">
10</p>

   </td>
   <td><p dir="rtl">
1</p>

   </td>
  </tr>
</table>


Transaction 1 : 

SELECT SUM(price * quantity) FROM products;

<p dir="rtl">
النتيجة هتكون 20.</p>


<p dir="rtl">
وفي نفس الوقت، Transaction 2 عملت Insert row جديد فنفس الtable:</p>


Transaction 2 : 

INSERT INTO products(price, quantity) VALUES (20, 2);

<p dir="rtl">
لو Transaction 1 كررت نفس الquery النتيجة هتتغير وتبقى 60 ودا لانها دلوقتي بقت بتحسب علي 2 rows فتاني query بعد مكانت بتحسب علي row واحد فاول query وطبعا نفيس المشكلة لو كانت Transaction 2 عملت delete row  \
ودا هيعمل مشاكل زي Non-repeatable Reads بالظبط \
 \
ببساطة الفرق بين ال Phantom Reads و ال Non-repeatable Reads \
Phantom Reads بتحصل بسبب الInsert/Delete queries انما \
 الNon-repeatable Reads بتحصل بسبب الUpdate queries \
</p>


4. Lost Updates رابع مشكلة والاخيرة هي ال 

<p dir="rtl">
و دي بتحصل بسبب ال UPDATE Queries وهي ان الابديت بتاع Transaction 1 يعمل overwrite للابديت بتاع Transaction 2 </p>


<p dir="rtl">
طب دا بيحصل ازاي ؟</p>


<p dir="rtl">
هناخد نفس الproducts table كمثال </p>



<table>
  <tr>
   <td>quantity
   </td>
   <td>price
   </td>
   <td>id
   </td>
  </tr>
  <tr>
   <td><p dir="rtl">
2</p>

   </td>
   <td><p dir="rtl">
10</p>

   </td>
   <td><p dir="rtl">
1</p>

   </td>
  </tr>
</table>


Transaction 1 :

SELECT quantity from products where id = 1 

<p dir="rtl">
دي نتيجتها هتبقا 2 و في نفس الوقت </p>


Transaction 2 :

SELECT quantity from products where id = 1 

<p dir="rtl">
دي برده هتبقا 2 بس دلوقتي لو Transaction 1 عملت </p>


<p dir="rtl">
update لل quantity </p>


Transaction 1 :

UPDATE products SET quantity =  quantity + 5 where id = 1 

<p dir="rtl">
وفي نفس الوقت Transaction 2 عملت ابديت لل quantity برده</p>


Transaction 2 : 

UPDATE products SET quantity =quantity +  3 where id = 1 

<p dir="rtl">
ايه الي هيحصل هنا ؟</p>


<p dir="rtl">
لو عملنا بعد منخلص ال 2 transactions   </p>


<p dir="rtl">
  SELECT quantity from products where id = 1 هتبقا النتيجة ايه ؟ النتيجة هتبقا 5 وهنا هتظهر المشكلة </p>


<p dir="rtl">
طب الupdate بتاع Transaction 1 راح فين ؟</p>


<p dir="rtl">
الي حصل ان الupdate بتاع transaction 2 هيعمل overwrite علي الابديت بتاع transaction 1 !</p>


<p dir="rtl">
لانو Transaction 2 مش شايفه Transaction 1 </p>


<p dir="rtl">
ومش عارفه ان حصل ابديت تاني علي الfield دا فهي بالنسبالها ال quantity فعلا لسا ب 2 فالبتالي 2 + 3 = 5 فعلا </p>


<p dir="rtl">
طبعا دا بيعمل مشاكل برده فالdata consitenty  \
الLost Updates هي اخر Read Phenomena وتعتبر اكتر واحدة حلها مكلف </p>


<p dir="rtl">
 \
ال <strong>Read Phenomena </strong>بيتحلو ب<strong>Isolation levels</strong> مختلفة علي حسب كل Database Implementation  \
</p>


<p dir="rtl">
فيه 4 <strong>Isolation Level</strong> مختلفين كل واحد فيهم بيحل عدد مختلف من ال <strong>Read Phenomena </strong>وهما  \
</p>


***1 - Read Uncommitted***

***2 - Read Committed***

***3- Repeatable Read ***

***4 - Serializable***

<p dir="rtl">
كل Database بتستخدم Isolation Level مختلف كمثال  \
 Postgres بتسخدم ال <strong><em>Read Committed </em></strong></p>


<p dir="rtl">
Mysql بتسخدم ال  <strong><em>Repeatable Read \
</em></strong></p>


<p dir="rtl">
طبعا انت تقدر تحدد ال الIsolation Level فكل Transaction علي حسب احتياجك عادي </p>


SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;

<p dir="rtl">
القرار هنا بيرجعلك علي حسب انت محتاج ايه بالظبط ودا بيفرق طبعا لان فيه  \
Tradeoff ما بين ال isolation level والPerformance فاحتياجك وقرار انك تستخدم انهي isolation level هو الي هيفرق </p>


طبعا كل Isolation Level ليه شرح لوحدو وازاي بيتعمل وايه ال **Read Phenomena **والي بيحلها بس دا موضوع تاني  \
 \
References :  \
 \
Fundamentals of Database Engineering By Hussein Nasser

 \
[https://medium.com/@yogeshsheoran/read-phenomena-and-transaction-isolation-in-dbms-96171b3dcf62](https://medium.com/@yogeshsheoran/read-phenomena-and-transaction-isolation-in-dbms-96171b3dcf62) \
 \
https://www.geeksforgeeks.org/dbms/transaction-isolation-levels-dbms/ \

