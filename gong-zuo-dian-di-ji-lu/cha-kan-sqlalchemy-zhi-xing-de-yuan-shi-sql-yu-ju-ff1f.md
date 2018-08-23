SQLAlchemy打开SQL语句方法如下，echo=true将开启该功能：

engine = create_engine("<db_rul>", echo=True)
Flask-SQLAlchemy打开SQL方法如下：

app.config["SQLALCHEMY_ECHO"] = True