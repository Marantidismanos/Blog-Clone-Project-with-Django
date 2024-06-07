#This is a Project with Django Framework v5.0.6 using Bootstrap v5.3

The Project is a Blog Clone.
- Post Model:
  - It represents a blog post and contains fields such as author, title, text, created_date, and published_date.
  author is a ForeignKey field linking to the built-in Django User model, representing the author of the post.
  title is a CharField representing the title of the post.
  text is a TextField representing the content of the post.
  created_date is a DateTimeField representing the date and time when the post was created.
  published_date is a DateTimeField representing the date and time when the post was published. It allows null values and is set to null by default.
  It has methods like publish, approve_comments, and get_absolute_url.
  The __str__ method returns the title of the post.

   Comment Model:
        It represents a comment on a blog post and contains fields such as post, author, text, create_date, and approved_comment.
        post is a ForeignKey field linking to the Post model, representing the post to which the comment belongs.
        author is a CharField representing the author of the comment.
        text is a TextField representing the content of the comment.
        create_date is a DateTimeField representing the date and time when the comment was created.
        approved_comment is a BooleanField representing whether the comment has been approved or not, with a default value of False.
        It has methods like approve and get_absolute_url.
        The __str__ method returns the content of the comment.

Additionally, both models import necessary modules from Django, such as models, timezone, and reverse. The reverse function is used to generate URLs for views.
