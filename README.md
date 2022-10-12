# CBV-FBV-CHECK-STAFF_MEMBER-AND-LOGIN-REQUIRED
DJANGO-CBV-FBV CHECK-STAFF_MEMBER-AND-LOGIN-REQUIRED(simple lessons)

## FUNCTION BASED VIEW

Django's login_required function is used to secure views in your web applications by forcing the client to authenticate with a valid logged-in User.

login_required fuction django pass function name , next and redirect url 
```
def login_required(function=None, redirect_field_name=REDIRECT_FIELD_NAME, login_url=None):

    actual_decorator = user_passes_test(
        lambda u: u.is_authenticated,
        login_url=login_url,
        redirect_field_name=redirect_field_name,
    )
    if function:
        return actual_decorator(function)
    return actual_decorator

```

- How to use login_required decorator 

``` 
from django.contrib.auth.decorators import login_required 

@login_required(login_url='accounts:login')
def category(request,*args,**kwargs):
  return render(request,'admin/category/category.html')
  
 ```
  
 
- staff_member_required use have is staff authenticate 

```
from django.contrib.auth import REDIRECT_FIELD_NAME
from django.contrib.auth.decorators import user_passes_test


def staff_member_required(view_func=None, redirect_field_name=REDIRECT_FIELD_NAME, login_url="admin:login"):
    actual_decorator = user_passes_test(
        lambda u: u.is_active and u.is_staff,
        login_url=login_url,
        redirect_field_name=redirect_field_name,
    )
    if view_func:
        return actual_decorator(view_func)
    return actual_decorator    
 ```

- @staff_member_required
    
 ```
from django.contrib.admin.views.decorators import staff_member_required

@staff_member_required(login_url='accounts:login')
def user_address(request,*args,**kwargs):
  try:
    pk=kwargs.get('pk')
    user_pk = base64.b64decode(pk)
    pk = user_pk.decode('ascii')
    user=get_object_or_404(User,pk=pk)
    address=UserAddress.objects.filter(user=user)
    print(address)
    context={
      'address':address,
      'user':user
    }
    return render(request,'admin_/users/user-address.html',context)
  except:
    return render(request,'massages/404.html')
 ```
    
    
 ## CBV login_required VIEWS using
 
 ```
 from django.contrib.auth.mixins import LoginRequiredMixin

 class Category(LoginRequiredMixin,ListView):
    model=Category
    login_url='accounts:login'
    template_name = 'admin_/categories/category/category.html'
    
    def get_context_data(self, **kwargs):
        context=super().get_context_data(**kwargs)
        context['categories'] = category_list(self)
        context['category']=CategoryModel.objects.filter(level=0).exclude(record_status='deleted',is_active=False)
        return context  
  ```
  
  ## OR
  ```
   from django.utils.decorators import method_decorator
 from django.contrib.auth.decorators import login_required

 @method_decorator(login_required, name='dispatch')
   model=Category
   template_name = 'admin_/categories/category/category.html'
    
    def get_context_data(self, **kwargs):
        context=super().get_context_data(**kwargs)
        context['categories'] = category_list(self)
        context['category']=CategoryModel.objects.filter(level=0).exclude(record_status='deleted',is_active=False)
        return context 
  ```
  
  
  ## USE staff_member_required USING
  
  ```
  from django.contrib.admin.views.decorators import staff_member_required
from django.utils.decorators import method_decorator

  @method_decorator(staff_member_required(login_url='accounts:login'),name='dispatch')
class Category(ListView):
    model=Category
    template_name = 'admin_/categories/category/category.html'
    
    def get_context_data(self, **kwargs):
        context=super().get_context_data(**kwargs)
        context['categories'] = category_list(self)
        context['category']=CategoryModel.objects.filter(level=0).exclude(record_status='deleted',is_active=False)
        return context   
   ```
        
    
