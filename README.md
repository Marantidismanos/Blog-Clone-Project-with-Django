#This is a Project with Django Framework v5.0.6 using Bootstrap v5.3

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
