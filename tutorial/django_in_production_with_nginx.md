## প্রোডাকসান সার্ভার (Nginx) এবং জ্যাঙ্গো 
 জ্যাঙ্গো দিয়ে আমাদের কাজ করার সময় আমরা বিল্ট-ইন ডেভেলপমেন্ট সার্ভার ব্যবহার করতাম। এতে কাজ করতে সুবিধা হলেও এটা প্রডাকশন লেভেলে ব্যবহারের অনুপযোগী । তাই আমাদেরকে কোন প্রোদাকশান সার্ভার ব্যবহার করতে হয়। এই টিউটোরিয়ালে আমরা Nginx ব্যবহার করবো।

 যেহেতু সার্ভারে লিনাক্স ব্যবহৃত হয়, তাই আমরা লিনাক্স এই টিউটোরিয়ালে লিনাক্স ব্যবহার করবো। আপনারা  যারা windows ব্যবহার করেন কিংবা লিনাক্স ব্যবহারের পরও একটু সতর্ক থাকতে চান (যাতে আপনার working laptop এ ঝামেলা না হয় আরকি,... )  , তারা ভারচুয়াল বক্সে লিনাক্স ব্যবহার করতে পারেন। আমি নিজেও কিছুটা ঝামেলায় পরেছিলাম, তাই ভারচুয়াল বক্সে উবুন্টু সার্ভার ইন্সটল করে সেখানে  জ্যাঙ্গো প্রোডাকশান করেছিলাম। সুতরাং ঝামেলা কমাতে এটি একটু ভাল উপায়।

 ## ধাপসমুহ 
আমাদের ১টা জ্যাঙ্গো প্রোজেক্ট দরকার। ছোটখাটো হেলো ওয়ার্ল্ড জাতিয় হলেই চলবে। ধরুন প্রোজেক্টের নাম   `webapp`.
 এরপর settings ফাইলে অ্যাড করুন ঃ
 ```
    DEBUG = False
 ``` 
 ```
STATIC_ROOT = os.path.join(BASE_DIR, 'static/') 
```
, এরপর টার্মিনালে রান করুন 
``` 
$ manage.py collectstatic
```
এবার এই প্রোজেক্টকে ভারচুয়াল বক্সে আনতে হবে। আমি গিটহাব-এ কোড আপলোড করে ভারচুয়াল বক্সে ডাউনলোড করেছিলাম। 

২। উবুন্টুতে nginx ইন্সটল করুন:

``` 
$ sudo apt-get install nginx 
```
আমই প্রোজেক্টটাকে এখানে রেখেছি ঃ 
`
/home/(user-name)/
`
তাই আমার প্রোজেক্টের রুট ডাইরেক্টরি হলোঃ 
`
/home/(user-name)/(project-name)
` 
এবং আমার এই ক্ষেত্রে তা ঃ 
`
/home/fahimfarhan/webapp
`
আপনার ক্ষেত্রে প্রয়োজন মতো পরিবর্তন করে নিবেন, নাহলে ঠিক মতো কাজ করবে না। 

৩। `
$ cd /etc/nginx/sites-available 
`
এই লোকেশানে যান, এখানে কিছু কাজ করতে হবে। এই লকেশানে পারমিশন-এর ব্যাপার আছে, তাই আমাদেরকে sudo 
`
sudo
` ব্যবহার করতে হবে। 
```
$ sudo touch (project-name)
```
আমার খেত্রেঃ 
```
$ sudo touch webapp
```
এবার webapp ফাইলটি ওপেন করুন (sudo ব্যবহার করতে হবে, যেমন টার্মিনাল থেকে `sudo gedit webapp` / `sudo vim webapp`  )  এবং টাইপ  করুনঃ 

 ```
 server {
    listen 8000;
    server_name 0.0.0.0;

    location = /favicon.ico { access_log off; log_not_found off; }

    location /static/ {
            root /home/(user-name)/(project);
    }

    location / {
            include proxy_params;
            proxy_pass http://unix:/home/(user-name)/(project)/(project).sock;
    }
}
 ```
এখানে (user-name) ও (project) প্রয়োজন মতো পরিবর্তন করুন। অনেক সময় পোর্ট নম্বর পরিবর্তন করা লাগতে পারে। 
এবার টার্মিনাল এ এই কমান্ড রান করে একটি লিঙ্ক তইরি করুন ঃ 
``` 
$ sudo ln -s /etc/nginx/sites-available/webapp /etc/nginx/sites-enabled 
```
এবার nginx রিস্টার্ট করুন ঃ
``` 
$ sudo service nginx restart 
```
আপনার প্রোজেক্ট রুট ডাইরেক্টরিতে যানঃ 
```
 $ cd ~/webapp/
 ```
  nginx দিয়ে সিএসএস , জাভাস্ক্রিপ্ট সারভ হয়, আর আমাদের মুল প্রোজেক্ট রান করতে gunicorn দরকার। তাই গুনিকরন না থাকলে ইন্সটল করুন 
  ```
$ pip install gunicorn 
# In my pc, python3 is default. so it works. If you have python 2 as default,
# use: pip3 install gunicorn 
  ```

 এবং এই কমান্ড টাইপ করুন ঃ 
```
$ gunicorn --daemon --workers 1 --bind unix:/home/(username)/(project)/(project).sock (project).wsgi 
 ```
এবং আমার ক্ষেত্রে কমান্ডটি দেখতে এরকম ঃ 
```
$ gunicorn3 --daemon --workers 1 --bind unix:/home/fahimfarhan/webapp/webapp.sock webapp.wsgi 
 ```

 এইভাবে আমরা  nginx  দিয়ে আমাদের প্রোজেক্টকে প্রোডাকশানে ব্যবহার করতে পারি। 
 ## রেফারেন্সঃ 
1. Install ubuntu server: [https://hibbard.eu/install-ubuntu-virtual-box/](https://hibbard.eu/install-ubuntu-virtual-box/)
2. django with nginx: [http://rahmonov.me/posts/run-a-django-app-with-nginx-and-gunicorn/](http://rahmonov.me/posts/run-a-django-app-with-nginx-and-gunicorn/)
3. youtube tutorial: [https://youtu.be/4Ouc1VPNpMU](https://youtu.be/4Ouc1VPNpMU)

 