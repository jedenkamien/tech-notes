## Transaction example in SQL Server

_How to easily delete all records from ParentTable for particular collection of someId-s (documentIds etc.), together with its children and grandchildren, when the OnDelete behaviour is not defined in the DbContext?_

If the OnDelete behavior is not defined (i.e., no cascading delete is set up in your EF Core model or database), you must manually delete the child and grandchild records before deleting the parent records to avoid foreign key constraint violations.

#### Example Transaction (SQL Server)
 
```sql
BEGIN TRANSACTION;

-- Delete grandchildren
DELETE gc
FROM GrandchildTable gc
INNER JOIN ChildTable c ON gc.ChildId = c.ChildId
INNER JOIN ParentTable p ON c.ParentId = p.ParentId
WHERE p.SomeForeignKey IN (1,2,3);

-- Delete children
DELETE c
FROM ChildTable c
INNER JOIN ParentTable p ON c.ParentId = p.ParentId
WHERE p.SomeForeignKey IN (1,2,3);

-- Delete parents
DELETE FROM ParentTable
WHERE SomeForeignKey IN (1,2,3);

COMMIT TRANSACTION;
```

#### Notes
-	Order matters: Always delete grandchildren first, then children, then parents.
-	No cascade: Since OnDelete is not set to Cascade, the database will not automatically delete child/grandchild records.
-	Transactional safety: Wrap these statements in a transaction to ensure atomicity.
