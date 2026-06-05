# All-weatherFarming
Farming app according to their weather conditions and market strategy.
import requests

API_KEY = "YOUR_OPENWEATHER_API_KEY"
CITY = "Sambalpur_Odisha_India"

url = f"https://api.openweathermap.org/data/2.5/weather?q={CITY}&appid={API_KEY}&units=metric"

response = requests.get(url)
data = response.json()

temp = data["main"]["temp"]
humidity = data["main"]["humidity"]
weather = data["weather"][0]["main"]

print(f"Temperature: {temp}°C")
print(f"Humidity: {humidity}%")
print(f"Weather: {weather}")

if temp > 35:
    print("Recommendation: Increase irrigation.")
elif weather.lower() == "rain":
    print("Recommendation: Stop irrigation and monitor drainage.")
elif humidity > 80:
    print("Recommendation: Check for fungal diseases.")
else:
    print("Recommendation: Normal farming operations.")












Building a complete All-Weather Farming Platform for India is a large project. A production-grade system would include:

Features
JWT Authentication
Session Management
Login/Register
Role-based Access (Farmer, Expert, Admin)
Weather-based Crop Advisory
Region-wise Farming Recommendations
Season-wise Farming Guide
Disease Detection Module
Irrigation Planning
Market Price Dashboard
Navigation Dashboard
Mobile Responsive UI
Recommended Technology Stack

Frontend

React.js
React Router
Axios
Material UI / Bootstrap

Backend

Node.js
Express.js
JWT Authentication
Express Session

Database

MongoDB

Weather APIs

OpenWeatherMap
IMD APIs (if available)
Project Structure
all-weather-farming/
│
├── backend/
│   ├── server.js
│   ├── config/
│   │    └── db.js
│   ├── models/
│   │    ├── User.js
│   │    ├── Crop.js
│   │    └── Region.js
│   ├── routes/
│   │    ├── authRoutes.js
│   │    ├── weatherRoutes.js
│   │    └── cropRoutes.js
│   ├── middleware/
│   │    └── authMiddleware.js
│   └── controllers/
│
└── frontend/
    ├── src/
    │   ├── pages/
    │   ├── components/
    │   ├── App.js
    │   └── index.js
MongoDB User Model
const mongoose = require('mongoose');

const userSchema = new mongoose.Schema({
    name:{
        type:String,
        required:true
    },
    email:{
        type:String,
        unique:true
    },
    password:{
        type:String,
        required:true
    },
    role:{
        type:String,
        default:'farmer'
    }
});

module.exports = mongoose.model('User', userSchema);
JWT Authentication
const jwt = require("jsonwebtoken");

const generateToken = (id) => {
    return jwt.sign(
        { id },
        process.env.JWT_SECRET,
        { expiresIn: "7d" }
    );
};

module.exports = generateToken;
Session Configuration
const session = require('express-session');

app.use(
 session({
   secret:'farmingsecret',
   resave:false,
   saveUninitialized:false,
   cookie:{
      maxAge:86400000
   }
 })
);
Login Controller
const bcrypt = require('bcryptjs');
const User = require('../models/User');
const jwt = require('jsonwebtoken');

exports.loginUser = async(req,res)=>{

 const {email,password}=req.body;

 const user = await User.findOne({email});

 if(!user){
   return res.status(404).json({
      message:"User not found"
   });
 }

 const isMatch = await bcrypt.compare(
      password,
      user.password
 );

 if(!isMatch){
    return res.status(401).json({
       message:"Invalid Password"
    });
 }

 const token = jwt.sign(
  {id:user._id},
  process.env.JWT_SECRET,
  {expiresIn:'7d'}
 );

 res.json({
   token,
   user
 });
};
Authentication Middleware
const jwt = require('jsonwebtoken');

module.exports = (req,res,next)=>{

 let token =
 req.headers.authorization;

 if(!token){
   return res.status(401)
   .json({message:'Unauthorized'});
 }

 token = token.split(" ")[1];

 try{

   const decoded =
   jwt.verify(
      token,
      process.env.JWT_SECRET
   );

   req.user = decoded;

   next();

 }catch(err){

   res.status(401)
   .json({message:'Invalid Token'});
 }
}
Weather Route
const express = require('express');
const router = express.Router();
const axios = require('axios');

router.get('/:city', async(req,res)=>{

 try{

   const city = req.params.city;

   const weather =
   await axios.get(
   `https://api.openweathermap.org/data/2.5/weather?q=${city}&appid=${process.env.WEATHER_API}`
   );

   res.json(weather.data);

 }catch(err){
   res.status(500).json(err);
 }
});

module.exports = router;
Crop Recommendation Model
const mongoose = require('mongoose');

const cropSchema = new mongoose.Schema({

 region:String,
 season:String,
 weather:String,

 crops:[
   String
 ]

});

module.exports =
mongoose.model(
'Crop',
cropSchema
);
Sample Crop Data
{
  "region":"North India",
  "season":"Winter",
  "weather":"Cold",
  "crops":[
     "Wheat",
     "Mustard",
     "Barley"
  ]
}
Farming Recommendation Logic
function getCropAdvice(
 region,
 season,
 weather
){

 if(region==="North India"
   && season==="Winter"){

   return [
      "Wheat",
      "Mustard",
      "Barley"
   ];
 }

 if(region==="South India"
    && season==="Monsoon"){

   return [
      "Rice",
      "Sugarcane",
      "Turmeric"
   ];
 }

 return [
    "Consult Agriculture Expert"
 ];
}
React Login Page
import React,{useState} from 'react';
import axios from 'axios';

function Login(){

 const[email,setEmail]=useState('');
 const[password,setPassword]=useState('');

 const login=async()=>{

  const res=
  await axios.post(
  "http://localhost:5000/api/login",
  {
    email,
    password
  });

 localStorage.setItem(
  "token",
  res.data.token
 );

 };

 return(

  <div>

   <h2>Farmer Login</h2>

   <input
    type="email"
    placeholder="Email"
    onChange={(e)=>
    setEmail(e.target.value)}
   />

   <input
    type="password"
    placeholder="Password"
    onChange={(e)=>
    setPassword(e.target.value)}
   />

   <button onClick={login}>
      Login
   </button>

  </div>
 );
}

export default Login;
React Navigation Page
import {
 BrowserRouter,
 Routes,
 Route,
 Link
} from 'react-router-dom';

function Navbar(){

 return(

  <nav>

   <Link to="/">Home</Link>

   <Link to="/weather">
      Weather
   </Link>

   <Link to="/crops">
      Crops
   </Link>

   <Link to="/disease">
      Disease
   </Link>

   <Link to="/market">
      Market
   </Link>

  </nav>

 );
}
Region Wise Farming Knowledge
Region	Season	Recommended Crops
North India	Winter	Wheat, Mustard, Barley
North India	Summer	Maize, Cotton
South India	Monsoon	Rice, Sugarcane
South India	Winter	Groundnut
East India	Monsoon	Paddy
West India	Summer	Bajra, Cotton
Himalayan Region	Cold	Apple, Potato
Dashboard Menu
Dashboard
├── Weather Forecast
├── Current Temperature
├── Rainfall Prediction
├── Crop Recommendation
├── Season Wise Guide
├── Region Wise Guide
├── Soil Analysis
├── Fertilizer Recommendation
├── Disease Detection
├── Pest Alert
├── Irrigation Planning
├── Market Price
├── Government Schemes
├── Farmer Community
├── Profile
└── Logout
Main App Routing
<Routes>

 <Route path="/"
 element={<Dashboard/>}/>

 <Route path="/login"
 element={<Login/>}/>

 <Route path="/weather"
 element={<Weather/>}/>

 <Route path="/crops"
 element={<CropPage/>}/>

 <Route path="/market"
 element={<MarketPage/>}/>

 <Route path="/profile"
 element={<Profile/>}/>

</Routes>

This provides the core architecture for an All-Weather Farming Management System for India using Node.js + Express + MongoDB + React, with JWT authentication, session management, login page, navigation, and region/season/weather-based crop recommendations. For a real deployment, additional modules such as IMD weather integration, GIS mapping, AI crop advisory, multilingual support (Hindi, English, regional languages), and mobile apps can be added.
