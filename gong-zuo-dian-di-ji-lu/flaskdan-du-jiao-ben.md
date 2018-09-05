__init__文件里
```python
    app.register_blueprint(subscribe_redeem, url_prefix='/subscribe_redeem')
    configure_uploads(app, uploaded_photos)
    mail.init_app(app)
    db.init_app(app)
    with app.app_context():
        # Extensions like Flask-SQLAlchemy now know what the "current" app
        # is while within this block. Therefore, you can now run........
        db.create_all()
```
![](/assets/333.png)