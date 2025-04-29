

# ✅ Django To-Do List App – README

## 📌 Project Overview
A simple Django To-Do List application that allows users to:
- Add tasks
- Mark tasks as complete
- Delete tasks
- View task details

This app is perfect for learning Django basics: models, views, templates, URLs, and template tags.

---



## 🧱 Models (`todo_app/models.py`)
```python
from django.db import models

class Todo(models.Model):
    title = models.CharField(max_length=200)
    description = models.TextField(blank=True)
    is_completed = models.BooleanField(default=False)
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.title
```

---

## 🔗 URLs (`todo_app/urls.py`)
```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.todo_list, name='todo-list'),
    path('add/', views.add_todo, name='add-todo'),
    path('complete/<int:id>/', views.mark_complete, name='mark-complete'),
    path('delete/<int:id>/', views.delete_todo, name='delete-todo'),
]
```

---

## 👁️ Views (`todo_app/views.py`)
```python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Todo

def todo_list(request):
    todos = Todo.objects.all().order_by('-created_at')
    return render(request, 'index.html', {'todos': todos})

def add_todo(request):
    if request.method == 'POST':
        title = request.POST.get('title')
        description = request.POST.get('description')
        Todo.objects.create(title=title, description=description)
        return redirect('todo-list')
    return render(request, 'add.html')

def mark_complete(request, id):
    todo = get_object_or_404(Todo, id=id)
    todo.is_completed = True
    todo.save()
    return redirect('todo-list')

def delete_todo(request, id):
    todo = get_object_or_404(Todo, id=id)
    todo.delete()
    return redirect('todo-list')
```

---

## 📄 Templates

### `base.html`
```html
<!DOCTYPE html>
<html>
<head>
    <title>Todo List</title>
</head>
<body>
    <h1>{% block title %}My Todo List{% endblock %}</h1>
    <hr>
    {% block content %}{% endblock %}
</body>
</html>
```

### `index.html`
```django
{% extends 'base.html' %}
{% block content %}
  <a href="{% url 'add-todo' %}">Add New Todo</a>
  <ul>
    {% for todo in todos %}
      <li>
        {{ todo.title }} - {{ todo.description }}
        {% if todo.is_completed %}
          ✅ Completed
        {% else %}
          <a href="{% url 'mark-complete' todo.id %}">Mark Complete</a>
        {% endif %}
        <a href="{% url 'delete-todo' todo.id %}">Delete</a>
      </li>
    {% endfor %}
  </ul>
{% endblock %}
```

### `add.html`
```django
{% extends 'base.html' %}
{% block content %}
  <form method="POST">
    {% csrf_token %}
    <label>Title:</label>
    <input type="text" name="title" required>
    <br>
    <label>Description:</label>
    <textarea name="description"></textarea>
    <br>
    <button type="submit">Add Todo</button>
  </form>
{% endblock %}
```

---

## ✅ Features Summary

| Feature | Description |
|--------|-------------|
| Add Todo | Create a new to-do task |
| View Todo List | Display all tasks |
| Mark Complete | Update status of a task |
| Delete Task | Remove task permanently |

---

