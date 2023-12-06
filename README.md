from flask import Flask, request, session, g, redirect, url_for, flash
from flask_wtf import FlaskForm
from wtforms import StringField, SubmitField, IntegerField
from wtforms.validators import DataRequired
from mongoengine import Document, StringField, IntField

app = Flask(__name__)
app.config['SECRET_KEY'] = 'secret_key_here'

class Product(Document):
    name = StringField()
    price = IntField()
    description = StringField()

class Order(Document):
    customer_name = StringField()
    customer_email = StringField()
    products = ListField(EmbeddedDocumentField(Product))

class OrderForm(FlaskForm):
    customer_name = StringField('Customer Name', validators=[DataRequired()])
    customer_email = StringField('Customer Email',=[DataRequired()])
    products = StringField('Products', validators=[DataRequired()])
    submit = SubmitField('Place Order')

@app.route('/')
def index():
    products = Product.objects()
    return render_template('index.html', products=products)

@app.route('/=['GET', 'POST'])
def order():
    form = OrderForm()
    if form.validate_on_submit():
        customer_name = form.customer_name.data
        customer_email = form.customer_email.data
        products = form.products.data
        order = Order(customer_name, customer_email, products)
        order.save()
        flash('Order placed successfully!        return redirect(url_for('index'))
    return render_template('order.html', form=form)

if __name__ == '__main__':
    app.run(debug=True)

