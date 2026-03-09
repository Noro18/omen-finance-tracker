# **SYSTEM MODELLING – Personal Finance Tracker**

## **1️⃣ Use Case Diagram (Aktor & Aksaun User)**

	

---

iha parte ne’e ita sei hatudu oinsa mak user bele interaje ho sistema, no asaun-prinsipal ne’ebé user bele halo.

**Aktor:** User

**User tenke bele halo:**

* Login / Register

* Aumenta **income**

* Aumenta **expense**

* Hamoos **transaksaun**

* Haree **lista transaksaun**

* Halo **budget** kada kategori

* Haree **restu budget**

* Haree **saldo atual**

* Haree **relatóriu fulan**

* Haree **predisaun finansa**

**Sistema tenke bele:**

* Guarda dadus iha database

* Kalkula total income

* Kalkula total expense

* Kalkula saldo atual

* Kalkula restu budget

* Halo predisaun finansa

---

## **2️⃣ Activity Diagram (Fluxu Sistem)**

---

"Iha parte ne’e, Activity Diagram sei hatudu fluxu atividade iha Personal Finance Tracker, hodi klaru oinsa mak user interaje ho sistema no oinsa mak sistem responde iha kada etapa.

1. User login → dashboard

2. Pilih menu:

   * Add Income

   * Add Expense

   * Set Budget

   * View Transactions

3. Input data iha form

4. Sistem simpan iha database

5. Sistem kalkula:

   * total income

   * total expense

   * saldo atual

   * restu budget

6. Dashboard hatudu:

   * transaksaun foun

   * saldo atual

   * restu budget

   * predisaun finansa

## **3️⃣ DATA MODELLING (ERD)**

---

* User				

  id

  username

  email

  password

* Transaction  
  	id  
  	user\_id  
  	type  
  	category  
  	amount  
  	date  
* Budget  
  	id  
  	user\_id  
  	category  
  	amount  
  	restu  
  	date

**relasaun:**

	**User—-\>Transaction(user 1 bele iha transaksaun barak)**

	**User—-\>Budget(user 1 bele iha budget barak)**

