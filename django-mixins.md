# Django Mixins

A Mixin is a special kind of inheritance in Python (and other object-oriented languages) and it’s starting to get a big rise in Django / Web Application Development. You can use a Mixin to allow classes in Python to share methods between any class that inherits from that Mixin. So what does it look like?

> A mixin is a just another class defined by you whose methods can be inherited by your view or any other class.

## Introduction

Some general rules to use mixins to compose your own view classes:

- The base view classes provided by Django always go to the right
- Mixins go to the left of the base view
- Mixins should inherit from Python’s built-in object type

## Let's start an example to illustrate the rules

```python
class FormMessageMixin(object):
    @property
    def form_valid_message(self):
        return NotImplemented

    form_invalid_message = 'Please correct the errors below.'

    def form_valid(self, form):
        messages.success(self.request, self.form_valid_message)
        return super(FormMessageMixin, self).form_valid(form)

    def form_invalid(self, form):
        messages.error(self.request, self.form_invalid_message)
        return super(FormMessageMixin, self).form_invalid(form)


class DocumentCreateView(FormMessageMixin, CreateView):
    model = Document
    fields = ('name', 'file')
    success_url = reverse_lazy('documents')
    form_valid_message = 'The document was successfully created!'
```

> In a similar way you could reuse the same FormMessageMixin in a UpdateView for example, and also override the default form_invalid_message

```python
class DocumentUpdateView(FormMessageMixin, UpdateView):
    model = Document
    fields = ('name', )
    success_url = reverse_lazy('documents')
    form_valid_message = 'The document was successfully updated!'
    form_invalid_message = 'There are some errors in the form below.'
```
