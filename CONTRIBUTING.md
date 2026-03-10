# Django Coding Style Guide

**File ida ne'e sei iha lingua Ingels tanba hay copy langusng husi AI no feel free par atu translate ba Tetum. Ingat harus branch baru PR**

> Follow these rules throughout the project so everyone writes consistent, clean code.

---

## 1. Imports — Order Matters

Always group imports in this order, with a blank line between each group:

```python
# 1. Python standard library
import os
import json

# 2. Django imports
from django.db import models
from django.contrib.auth.models import User

# 3. Third party libraries
import pandas as pd

# 4. Your own apps
from apps.transactions.models import Transaction
```

---

## 2. Models

- Class names are **PascalCase** (capitalize each word)
- Field names are **lowercase_with_underscores**
- Always add `__str__` so objects show readable names in the admin panel
- Always add `class Meta` for ordering and verbose names
- Choices go at the top of the class as constants

```python
class Transaction(models.Model):

    # choices go at the top as constants
    INCOME = 'income'
    EXPENSE = 'expense'
    TYPE_CHOICES = [
        (INCOME, 'Income'),
        (EXPENSE, 'Expense'),
    ]

    user = models.ForeignKey(User, on_delete=models.CASCADE)
    amount = models.DecimalField(max_digits=10, decimal_places=2)
    transaction_type = models.CharField(max_length=10, choices=TYPE_CHOICES)
    description = models.CharField(max_length=255, blank=True)
    date = models.DateField()
    created_at = models.DateTimeField(auto_now_add=True)

    class Meta:
        ordering = ['-date']           # newest first
        verbose_name = 'Transaction'
        verbose_name_plural = 'Transactions'

    def __str__(self):
        return f"{self.transaction_type} - {self.amount} on {self.date}"
```

---

## 3. Views

- Function names are **lowercase_with_underscores**
- Keep views thin — no heavy logic inside, move that to models or a separate `services.py`
- Use **Class Based Views** for CRUD operations
- Use **Function Based Views** for simple or custom logic

```python
# Function Based View — use for simple pages
def dashboard(request):
    transactions = Transaction.objects.filter(user=request.user)
    return render(request, 'dashboard/index.html', {'transactions': transactions})


# Class Based View — use for CRUD (less code, cleaner)
from django.views.generic import ListView, CreateView, UpdateView, DeleteView

class TransactionListView(ListView):
    model = Transaction
    template_name = 'transactions/list.html'
    context_object_name = 'transactions'

    def get_queryset(self):
        return Transaction.objects.filter(user=self.request.user)


class TransactionCreateView(CreateView):
    model = Transaction
    fields = ['amount', 'transaction_type', 'category', 'description', 'date']
    template_name = 'transactions/add.html'
    success_url = '/transactions/'
```

**Rule of thumb:**
| Situation | Use |
|---|---|
| Simple page, no form | Function Based View |
| Create, Read, Update, Delete | Class Based View |
| Custom logic, multiple models | Function Based View |

---

## 4. URLs

- URL names are **lowercase-with-hyphens**
- Always name your URLs using `name=`
- Always add `app_name` to namespace your URLs
- Each app has its own `urls.py`

```python
# apps/transactions/urls.py
from django.urls import path
from . import views

app_name = 'transactions'  # namespace — important!

urlpatterns = [
    path('', views.TransactionListView.as_view(), name='list'),
    path('add/', views.TransactionCreateView.as_view(), name='add'),
    path('<int:pk>/edit/', views.TransactionUpdateView.as_view(), name='edit'),
    path('<int:pk>/delete/', views.TransactionDeleteView.as_view(), name='delete'),
]
```

Include app URLs in the root `core/urls.py`:

```python
# core/urls.py
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('transactions/', include('apps.transactions.urls')),
    path('accounts/', include('apps.accounts.urls')),
    path('dashboard/', include('apps.dashboard.urls')),
    path('budgets/', include('apps.budgets.urls')),
    path('predictions/', include('apps.predictions.urls')),
    path('notifications/', include('apps.notifications.urls')),
]
```

In templates, always reference URLs by name — **never hardcode paths:**

```html
<!-- ❌ Bad -->
<a href="/transactions/add/">Add</a>

<!-- ✅ Good -->
<a href="{% url 'transactions:add' %}">Add</a>
```

---

## 5. Templates

- Template file names are **lowercase_with_underscores**
- Always extend `base.html` at the top
- Never put logic or calculations in templates — only display data
- Use `{% empty %}` inside for loops to handle empty states

```html
{% extends "base.html" %}

{% block title %}Transactions{% endblock %}

{% block content %}
    <h2 class="text-2xl font-bold">My Transactions</h2>

    {% for transaction in transactions %}
        <div class="p-4 bg-white rounded shadow mb-2">
            <p>{{ transaction.description }} — {{ transaction.amount }}</p>
        </div>
    {% empty %}
        <p class="text-gray-500">No transactions yet.</p>
    {% endfor %}
{% endblock %}
```

**Available blocks in `base.html`:**
| Block | Purpose |
|---|---|
| `{% block title %}` | Sets the browser tab title |
| `{% block content %}` | Main page content goes here |
| `{% block extra_css %}` | Page-specific styles |
| `{% block extra_js %}` | Page-specific JavaScript |

---

## 6. Static Files

- Each app has its own `static/appname/` folder for app-specific files
- Project-wide static files go in the root `static/` folder

```
finance-tracker/
├── static/                  ← project-wide static files
│   └── css/
│       └── main.css
├── apps/
│   └── transactions/
│       └── static/
│           └── transactions/
│               └── transactions.js   ← app-specific static files
```

Reference static files in templates like this:

```html
{% load static %}
<link rel="stylesheet" href="{% static 'css/main.css' %}">
<script src="{% static 'transactions/transactions.js' %}"></script>
```

---

## 7. Settings

Never push sensitive information to GitHub. Use environment variables for secrets:

```python
import os

# Never hardcode this
SECRET_KEY = os.environ.get('SECRET_KEY', 'local-dev-key-only')

# Never set this to True in production
DEBUG = True

# Never hardcode database passwords
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}
```

---

## 8. Git Rules

- **Never commit directly to `main`** — always work on a feature branch
- One branch per feature or app

```bash
# Always create a branch before starting work
git checkout -b feature/transactions
git checkout -b feature/dashboard
git checkout -b feature/predictions
```

- Write clear commit messages:

```bash
# ❌ Bad
git commit -m "fix"
git commit -m "update"

# ✅ Good
git commit -m "add transaction model and migrations"
git commit -m "fix budget overspending alert logic"
git commit -m "add spending prediction view"
```

---

## 9. Golden Rules Summary

| Rule | Example |
|---|---|
| Models = PascalCase | `class Transaction` |
| Fields and functions = snake_case | `transaction_type`, `get_total()` |
| Always add `__str__` to every model | `return f"{self.amount}"` |
| Never hardcode URLs in templates | `{% url 'transactions:add' %}` |
| Keep views thin | Move logic to `models.py` or `services.py` |
| One app = one responsibility | `transactions` app only handles transactions |
| Name every URL | `name='list'`, `name='add'` |
| Never commit secrets to GitHub | Use `os.environ.get()` |
| Always work on a feature branch | `git checkout -b feature/transactions` |

---

## 10. Folder Structure Reference

```
# Django Coding Style Guide
> Follow these rules throughout the project so everyone writes consistent, clean code.

---

## 1. Imports — Order Matters

Always group imports in this order, with a blank line between each group:

```python
# 1. Python standard library
import os
import json

# 2. Django imports
from django.db import models
from django.contrib.auth.models import User

# 3. Third party libraries
import pandas as pd

# 4. Your own apps
from apps.transactions.models import Transaction
```

---

## 2. Models

- Class names are **PascalCase** (capitalize each word)
- Field names are **lowercase_with_underscores**
- Always add `__str__` so objects show readable names in the admin panel
- Always add `class Meta` for ordering and verbose names
- Choices go at the top of the class as constants

```python
class Transaction(models.Model):

    # choices go at the top as constants
    INCOME = 'income'
    EXPENSE = 'expense'
    TYPE_CHOICES = [
        (INCOME, 'Income'),
        (EXPENSE, 'Expense'),
    ]

    user = models.ForeignKey(User, on_delete=models.CASCADE)
    amount = models.DecimalField(max_digits=10, decimal_places=2)
    transaction_type = models.CharField(max_length=10, choices=TYPE_CHOICES)
    description = models.CharField(max_length=255, blank=True)
    date = models.DateField()
    created_at = models.DateTimeField(auto_now_add=True)

    class Meta:
        ordering = ['-date']           # newest first
        verbose_name = 'Transaction'
        verbose_name_plural = 'Transactions'

    def __str__(self):
        return f"{self.transaction_type} - {self.amount} on {self.date}"
```

---

## 3. Views

- Function names are **lowercase_with_underscores**
- Keep views thin — no heavy logic inside, move that to models or a separate `services.py`
- Use **Class Based Views** for CRUD operations
- Use **Function Based Views** for simple or custom logic

```python
# Function Based View — use for simple pages
def dashboard(request):
    transactions = Transaction.objects.filter(user=request.user)
    return render(request, 'dashboard/index.html', {'transactions': transactions})


# Class Based View — use for CRUD (less code, cleaner)
from django.views.generic import ListView, CreateView, UpdateView, DeleteView

class TransactionListView(ListView):
    model = Transaction
    template_name = 'transactions/list.html'
    context_object_name = 'transactions'

    def get_queryset(self):
        return Transaction.objects.filter(user=self.request.user)


class TransactionCreateView(CreateView):
    model = Transaction
    fields = ['amount', 'transaction_type', 'category', 'description', 'date']
    template_name = 'transactions/add.html'
    success_url = '/transactions/'
```

**Rule of thumb:**
| Situation | Use |
|---|---|
| Simple page, no form | Function Based View |
| Create, Read, Update, Delete | Class Based View |
| Custom logic, multiple models | Function Based View |

---

## 4. URLs

- URL names are **lowercase-with-hyphens**
- Always name your URLs using `name=`
- Always add `app_name` to namespace your URLs
- Each app has its own `urls.py`

```python
# apps/transactions/urls.py
from django.urls import path
from . import views

app_name = 'transactions'  # namespace — important!

urlpatterns = [
    path('', views.TransactionListView.as_view(), name='list'),
    path('add/', views.TransactionCreateView.as_view(), name='add'),
    path('<int:pk>/edit/', views.TransactionUpdateView.as_view(), name='edit'),
    path('<int:pk>/delete/', views.TransactionDeleteView.as_view(), name='delete'),
]
```

Include app URLs in the root `core/urls.py`:

```python
# core/urls.py
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('transactions/', include('apps.transactions.urls')),
    path('accounts/', include('apps.accounts.urls')),
    path('dashboard/', include('apps.dashboard.urls')),
    path('budgets/', include('apps.budgets.urls')),
    path('predictions/', include('apps.predictions.urls')),
    path('notifications/', include('apps.notifications.urls')),
]
```

In templates, always reference URLs by name — **never hardcode paths:**

```html
<!-- ❌ Bad -->
<a href="/transactions/add/">Add</a>

<!-- ✅ Good -->
<a href="{% url 'transactions:add' %}">Add</a>
```

---

## 5. Templates

- Template file names are **lowercase_with_underscores**
- Always extend `base.html` at the top
- Never put logic or calculations in templates — only display data
- Use `{% empty %}` inside for loops to handle empty states

```html
{% extends "base.html" %}

{% block title %}Transactions{% endblock %}

{% block content %}
    <h2 class="text-2xl font-bold">My Transactions</h2>

    {% for transaction in transactions %}
        <div class="p-4 bg-white rounded shadow mb-2">
            <p>{{ transaction.description }} — {{ transaction.amount }}</p>
        </div>
    {% empty %}
        <p class="text-gray-500">No transactions yet.</p>
    {% endfor %}
{% endblock %}
```

**Available blocks in `base.html`:**
| Block | Purpose |
|---|---|
| `{% block title %}` | Sets the browser tab title |
| `{% block content %}` | Main page content goes here |
| `{% block extra_css %}` | Page-specific styles |
| `{% block extra_js %}` | Page-specific JavaScript |

---

## 6. Static Files

- Each app has its own `static/appname/` folder for app-specific files
- Project-wide static files go in the root `static/` folder

```
finance-tracker/
├── static/                  ← project-wide static files
│   └── css/
│       └── main.css
├── apps/
│   └── transactions/
│       └── static/
│           └── transactions/
│               └── transactions.js   ← app-specific static files
```

Reference static files in templates like this:

```html
{% load static %}
<link rel="stylesheet" href="{% static 'css/main.css' %}">
<script src="{% static 'transactions/transactions.js' %}"></script>
```

---

## 7. Settings

Never push sensitive information to GitHub. Use environment variables for secrets:

```python
import os

# Never hardcode this
SECRET_KEY = os.environ.get('SECRET_KEY', 'local-dev-key-only')

# Never set this to True in production
DEBUG = True

# Never hardcode database passwords
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}
```

---

## 8. Git Rules

- **Never commit directly to `main`** — always work on a feature branch
- One branch per feature or app

```bash
# Always create a branch before starting work
git checkout -b feature/transactions
git checkout -b feature/dashboard
git checkout -b feature/predictions
```

- Write clear commit messages:

```bash
# ❌ Bad
git commit -m "fix"
git commit -m "update"

# ✅ Good
git commit -m "add transaction model and migrations"
git commit -m "fix budget overspending alert logic"
git commit -m "add spending prediction view"
```

---

## 9. Golden Rules Summary

| Rule | Example |
|---|---|
| Models = PascalCase | `class Transaction` |
| Fields and functions = snake_case | `transaction_type`, `get_total()` |
| Always add `__str__` to every model | `return f"{self.amount}"` |
| Never hardcode URLs in templates | `{% url 'transactions:add' %}` |
| Keep views thin | Move logic to `models.py` or `services.py` |
| One app = one responsibility | `transactions` app only handles transactions |
| Name every URL | `name='list'`, `name='add'` |
| Never commit secrets to GitHub | Use `os.environ.get()` |
| Always work on a feature branch | `git checkout -b feature/transactions` |

---

## 10. Folder Structure Reference (Temporary)

```
finance-tracker/
├── core/                        ← project settings
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── apps/
│   ├── accounts/                ← auth (login, register, logout)
│   │   ├── templates/accounts/
│   │   ├── models.py
│   │   ├── views.py
│   │   └── urls.py
│   ├── dashboard/               ← main overview page
│   │   ├── templates/dashboard/
│   │   ├── views.py
│   │   └── urls.py
│   ├── transactions/            ← income and expense tracking
│   │   ├── templates/transactions/
│   │   ├── models.py
│   │   ├── views.py
│   │   └── urls.py
│   ├── budgets/                 ← budget limits per category
│   │   ├── templates/budgets/
│   │   ├── models.py
│   │   ├── views.py
│   │   └── urls.py
│   ├── predictions/             ← ML spending prediction
│   │   ├── templates/predictions/
│   │   ├── models.py
│   │   ├── views.py
│   │   └── urls.py
│   └── notifications/           ← overspending alerts
│       ├── templates/notifications/
│       ├── models.py
│       ├── views.py
│       └── urls.py
├── templates/                   ← base.html only
│   └── base.html
├── static/                      ← project-wide static files
├── manage.py
├── requirements.txt
└── .gitignore
```
```