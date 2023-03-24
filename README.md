# resturant app
'use strict';

var express = require('express');
var exphbs = require('express-handlebars');
var bodyParser = require('body-parser');
var session = require('express-session');
var flash = require('express-flash');
var app = express();

const Waiter = require('./waiter');
const models = require('./models/waiterModel');

const waiter = Waiter(models);


app.engine('handlebars', exphbs({
  defaultLayout: 'main'
}));

app.set('view engine', 'handlebars');

app.use(express.static('public'));
app.use(express.static('views'));
app.use(flash());

app.use(bodyParser.urlencoded({
  extended: false
}));
app.use(bodyParser.json())

app.use(session({
  secret: 'keyboard cat',
  cookie: {
    maxAge: 60000 * 30
  },
  resave: true,
  saveUninitialized: true
}));

var format = require('util').format;

app.get('/', function(req, res) {
  res.redirect('/waiters/add')
});

app.get('/waiters/:username', function(req, res) {
  res.render('waiters/add', {
    username: "Hello And Welcome " + req.params.username + " .Can You Please Select The Days You'll Be Available"
  });
});

app.post('/waiters/:username', waiter.getWaiter);
app.get('/days', waiter.admin);

const port = process.env.PORT || 3000;

app.use(function(err, req, res, next) {
  console.error(err.stack)
  res.status(500).send("Please Fill Out Required Fields")
});

app.listen(port, function() {
  console.log('WaiterApp started on port:' + port)
});
