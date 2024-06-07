--> This is a Project with Django Framework v5.0.6 using Bootstrap v5.3 <--
---------------------------------------------------------------------------

The Project is a Blog Clone.
----------------------------

models.py
---------

- Post Model:
  - It represents a blog post and contains fields such as author, title, text, created_date, and published_date.
  - author is a ForeignKey field linking to the built-in Django User model, representing the author of the post.
  - title is a CharField representing the title of the post.
  - text is a TextField representing the content of the post.
  - created_date is a DateTimeField representing the date and time when the post was created.
  - published_date is a DateTimeField representing the date and time when the post was published. It allows null values and is set to null by default.
  - It has methods like publish, approve_comments, and get_absolute_url.
  - The __str__ method returns the title of the post.

- Comment Model:
  - It represents a comment on a blog post and contains fields such as post, author, text, create_date, and approved_comment.
  - post is a ForeignKey field linking to the Post model, representing the post to which the comment belongs.
  - author is a CharField representing the author of the comment.
  - text is a TextField representing the content of the comment.
  - create_date is a DateTimeField representing the date and time when the comment was created.
  - approved_comment is a BooleanField representing whether the comment has been approved or not, with a default value of False.
  - It has methods like approve and get_absolute_url.
  - The __str__ method returns the content of the comment.

Additionally, both models import necessary modules from Django, such as models, timezone, and reverse. The reverse function is used to generate URLs for views.

Code from models.py:
--------------------
    from django.db import models
    from django.utils import timezone
    from django.urls import reverse
    # Create your models here.


    class Post(models.Model):
        author = models.ForeignKey('auth.User',on_delete=models.CASCADE)
        title = models.CharField(max_length=200)
        text = models.TextField()
        created_date = models.DateTimeField(default=timezone.now)
        published_date = models.DateTimeField(blank=True,null=True)

        def publish(self):
            self.published_date = timezone.now()
            self.save()

        def approve_comments(self):
            return self.comments.filter(approved_comment=True)
    
        def get_absolute_url(self):
            return reverse("post_detail",kwargs={'pk':self.pk})
    
        def __str__(self):
            return self.title
    


    class Comment(models.Model):
        post = models.ForeignKey('blog.Post',related_name='comments',on_delete=models.CASCADE)
        author = models.CharField(max_length=200)
        text = models.TextField()
        create_date = models.DateTimeField(default=timezone.now)
        approved_comment = models.BooleanField(default=False)

        def approve(self):
            self.approved_comment = True
            self.save()
    
        def get_absolute_url(self):
            return reverse("post_list")
        
        def __str__(self):
            return self.text


views.py
---------

- Import Statements:
  - Import necessary modules from Django such as render, get_object_or_404, redirect, etc.
  - Import models (Post and Comment) from the blog.models module.
  - Import forms (PostForm and CommentForm) from the blog.forms module.
  - Import other necessary modules like timezone, reverse_lazy, etc.

- Class-Based Views:
  - AboutView: A simple template view that renders the 'about.html' template.
  - PostListView: A ListView that displays a list of published posts ordered by their published dates.
  - PostDetailView: A DetailView that displays the details of a specific post.
  - CreatePostView: A CreateView that allows logged-in users to create new posts.
  - UpdatePostView: An UpdateView that allows logged-in users to update existing posts.
  - DeletePostView: A DeleteView that allows logged-in users to delete existing posts.

- LoginRequiredMixin:
  - Used as a mixin class with views that require the user to be logged in.

- DraftListView:
  - A ListView that displays a list of draft posts (posts with null published dates) ordered by their creation dates. Requires the user to be logged in.

- Function-Based Views:
  - post_publish: A view function that publishes a draft post.
  - add_comment_to_post: A view function that allows users to add comments to a specific post.
  - comment_approve: A view function that approves a comment.
  - comment_remove: A view function that removes a comment.

Each view or view function handles a specific HTTP request and performs appropriate actions, such as rendering templates, creating, updating, or deleting objects, and redirecting users to other pages. The login_required decorator is used to restrict access to certain views or view functions to logged-in users only.

Code from views.py:
-------------------
    from typing import Any


    from django.shortcuts import render,get_object_or_404,redirect
    from blog.models import Post,Comment
    from blog.forms import PostForm,CommentForm
    from django.utils import timezone
    from django.urls import reverse_lazy
    from django.contrib.auth.decorators import login_required
    from django.contrib.auth.mixins import LoginRequiredMixin
    from django.views.generic import (TemplateView,ListView,
                                  DetailView,CreateView,UpdateView,DeleteView)

    # Create your views here.

    class AboutView(TemplateView):
        template_name='about.html'


    class PostListView(ListView):
        model = Post

        def get_queryset(self):
            return Post.objects.filter(published_date__lte=timezone.now()).order_by('-published_date')
    

    class PostDetailView(DetailView):
        model = Post


    class CreatePostView(LoginRequiredMixin,CreateView):
        login_url = '/login/'
        redirect_field_name = 'blog/post_detail.html'

        form_class = PostForm

        model = Post

    class UpdatePostView(LoginRequiredMixin,UpdateView):
        login_url = '/login/'
        redirect_field_name = 'blog/post_detail.html'

        form_class = PostForm
        model = Post


    class DeletePostView(LoginRequiredMixin,DeleteView):
        model = Post
        success_url = reverse_lazy('post_list')

    
    class DraftListView(LoginRequiredMixin, ListView):
      login_url = '/login/'
      redirect_field_name = 'blog/post_list.html'
      model = Post

      def get_queryset(self):
          return Post.objects.filter(published_date__isnull=True).order_by('created_date')

    
    ##############################################
    ##############################################

    @login_required
    def post_publish(request,pk):
        post = get_object_or_404(Post,pk=pk)
        post.publish()
        return redirect('post_detail',pk=pk)



    @login_required
    def add_comment_to_post(request,pk):
        post = get_object_or_404(Post,pk=pk)
        if request.method == 'POST':
            form = CommentForm(request.POST)
            if form.is_valid():
                comment = form.save(commit=False)
                comment.post = post
                comment.save()
                return redirect('post_detail',pk=post.pk)
        else:
            form = CommentForm()
        return render(request,'blog/comment_form.html',{'form':form})


    @login_required
    def comment_approve(request,pk):
        comment = get_object_or_404(Comment,pk=pk)
        comment.approve()
        return redirect('post_detail',pk=comment.post.pk)


    @login_required
    def comment_remove(request,pk):
        comment = get_object_or_404(Comment,pk=pk)
        post_pk=comment.post.pk
        comment.delete()
        return redirect('post_detail',pk=post_pk)







