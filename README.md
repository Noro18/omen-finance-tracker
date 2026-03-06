# finance-tracker
Projetu final ba materia Software Engineering

## App Description

## 🔮 Omen — Advanced Personal Finance Tracker
**Omen** mak web-based manejamentu fiancial perosnal ne'ebe kria ho technologia **Django**. Nia funsaun prinsipal atu ajuda user kontorla sira nia osan liu husi monitora income, expensed no vizualiza habitu gastu osan, no mos halo prediksaun ba hahalok financial iha futuro ho machine Learning 

## Features

**Transaction Management**
User bele log nia income no expense, tau ba kategoria (hahan, transporte, rent, entertainment etc..) no fo sai historia transaksaun fulll iha fatin ida

**Interactive Dashboard**
Vizaun Geral ba saude financial - hatudu total income, osan sai, breakdown kada kategoria no trend fulan nian liu husi grafiku no chart

**Future Spending Prediction**
Liu husi Machine Learning model ne'ebe treinu ho user nia historia transaksaun, Omen bele halo prediksaun osan hira mak use sei hasai iha fulan oin bazeia ba kategoria

**Overspending Alerts & Reminders**
User bele halo budgeting ba kada categoria. No Omen bel automaikamente notifka ba user se quando spending ba kategoria ida nian liu ona nia limite

**User Authentication**
registrasaun seguro, login, logout, no manejamentu password. kada user nia dados privado sei isolado ba nia account deit 

**Admin Panel**
Admin Django nian atu maneja user, categoria transaksaun durante desenvolviment no testing 

## Tech Stack


| Layer                      |                          Tech                          | Description                         |
| :------------------------------ | :----------------------------------------------------: | ----------------------------------- |
| Front-End                       |            Django Templates + Tailwind CSS             | Chart.js for dashboard visualiation |
| Backend                         |            Django  + Django REST Framework             |                                     |
| Database                        |   SQLite for Developmt <br>PostgreSQL for Production   |                                     |
| Django Allauth                  | For Register, login, and password reset out of the box |                                     |
| Version Control & Collaboration |                      Git + Github                      |                                     |
| Prediction Model                |       Pandas + scikit-learn (Linear Regression)        |                                     |

## Team

| Role | Responsibility |
|---|---|
| Requirements Engineer | User stories, use cases, feature definitions |
| Software Developer | Django backend, models, views, prediction model |
| System Designer | Architecture, ER diagram, API design |
| System Tester | Test cases, bug reports, quality assurance |
| System Modeler | UML diagrams, system documentation |****

## Project Setup

Python version: `3.14.2`

Create environment:

```bash
python -m venv venv
source venv/bin/activate
```

Install dependencies:
```bash
pip install -r requirements.txt
```