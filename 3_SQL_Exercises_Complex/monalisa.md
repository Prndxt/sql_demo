# The Theft of the Mona Lisa

## The case

The Mona Lisa was stolen from the Louvre in Paris on **2014-10-23**. Your job is to find the thieves using SQL.

You will work step by step to find out:

- Who was in Paris on the day of the theft?
- Who was sending suspicious messages around that time?
- Who were the thieves?
- Bonus: who planned the crime, and who only helped?

Work in pairs. In DBeaver, set the `monalisa` schema as your default schema.

All dates in this exercise use the format `YYYY-MM-DD`, the same format you write in SQL.

These exercises are adapted to PostgreSQL from the [SQL tutorial by OpenTechSchool](http://opentechschool.github.io/sql-tutorial/).

## Part 1: Who was in Paris?

### 1. Explore the tables

Look at the table names and their columns. Which table could hold information about travel?

### 2. Explore the travel data

Look at the travel table. What information does it store, and what does each column mean?

### 3. Who lives in Paris?

Find the names of all people who live in Paris.

### 4. Who flew to Paris?

Find everyone who travelled **to** Paris **before** 2014-10-23.

### 5. Who left Paris?

Find everyone who travelled **from** Paris **after** 2014-10-23.

### 6. Who visited Paris during the theft?

Find the people who appear in both lists:

- they travelled to Paris before 2014-10-23, **and**
- they travelled from Paris after 2014-10-23.

These people were visiting Paris on the day of the theft.

### 7. Build the full suspect list

Find everyone who either:

- lives in Paris, **or**
- was visiting Paris on 2014-10-23 (your result from exercise 6).

This is your list of suspects.

## The alibi check

The local police will visit the visitors on your list to check their alibis. For that, they need each person's name and place of residence.

Extend your previous query in two steps:

1. Select from the flight table only the people who do **not** live in Paris.
2. For each person and their place of residence, check that there is a matching flight.

One person on the list has **no** matching flight. Who is this person, and why do they show up on your list?

Now check your result again. Do you really have the correct list of people who travelled to Paris before 2014-10-23 and from Paris after 2014-10-23?

<details>
<summary>Click here for a hint</summary>

Do not include people who live in Paris.

</details>

## Primary keys and foreign keys

Look at the `WHERE` clause of your last query. You joined the `person` and flight tables on the **name**. But names are not unique: two different people can have the same name. That is what caused the mistake.

To avoid this, tables use keys:

- A **primary key (PK)** is a column (or combination of columns) that is unique for every row in a table.
- A **foreign key (FK)** is a column in another table that stores the primary key of the row it refers to.

For example, `id` is the primary key of the `person` table. The `phone_contract` table refers to it through its foreign key `person_id`:

```
person.id = phone_contract.person_id
```

### 8. Find the foreign keys

Look at the table that contains the messages. Which columns are foreign keys, and which table does each one refer to?

> **Hint:** Primary key columns often end in `id`. Foreign key columns often include the name of the table they refer to. This is a common convention, but not every database follows it.

### 9. Fix your query

Run your alibi query again, but this time join the tables using the primary key and foreign key instead of the name. Does the result change?

## Part 2: Who is the thief?

The thief probably did not work alone. To find out who was involved, look at the text messages stored by the mobile phone providers.

### 10. Find the tables

What are the names of:

- the table that contains the text messages, and
- the table that contains the phone contracts?

### 11. Find the messages

Find all text messages sent between **2014-10-20 and 2014-10-25**.

### 12. Find the suspects' contracts

Find the IDs of all phone contracts that belong to the suspects from exercise 7. Match the contract's `person_id` to the suspects' `id`.

### 13. Find the suspects' messages

Find all text messages that:

- were sent between 2014-10-20 and 2014-10-25, **and**
- have a `contract_sender_id` that matches one of the suspects' contract IDs from exercise 12.

Your result will look messy. You can sort it with `ORDER BY`. For example, this query returns all messages from one day, sorted by time:

```sql
SELECT *
FROM <messages_table>
WHERE sent::date = '2014-10-21'
ORDER BY sent;
```

Replace `<messages_table>` with the table name you found in exercise 10.

> **Note:** `sent` stores a date and time, so we use `sent::date` to compare only the date part. `LIKE` only works on text, so `WHERE sent LIKE '2014-10-21%'` would fail in PostgreSQL.

### 14. Read the conversation

Take your query from exercise 13 and sort the messages by the time they were sent. Read them in order. Can you see a conversation?

### 15. Who are the thieves?

The messages leave a clear trace. For the messages that look suspicious, take the sender and receiver contract IDs and look up the people they belong to in the `person` table.

**Who are the thieves?**

If you found them, you deserve a break!

## Bonus: find the mastermind

Who pulled the strings, and who was only the henchman? If you want more SQL practice, continue the investigation in [Chapter 4 of the OpenTechSchool tutorial](http://opentechschool.github.io/sql-tutorial/chapter4.html).
