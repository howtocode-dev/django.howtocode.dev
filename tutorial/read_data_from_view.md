
## ভিউ থেকে ডাটাবেইজ এর ডাটা রিড করা
আমরা মডেল তৈরি করেছি, সেই মডেলে কিছু ডাটা/ফিল্ড তৈরি করেছি, এখন আমরা সেই ডাটাগুলো ভিউ এর মাধ্যমে প্রকাশ করব, যাতে কেউ আমাদের ওয়েবসাইটে () গেলে সেই ডাটাগুলো দখতে পায়।

আমরা আগের চ্যাপ্টারে দেখেছি কিভাবে জ্যাঙ্গো শেলের মাধ্যমে মডেল এর ডাটা রিড করা যায়, ভিউ তে আমরা একই ভাবে কাজটা করব।
আমাদের myapp এর ভিতরে থাকা views.py ফাইলটা টেক্সট এডিটরে ওপেন করুন, সেটার চেহারা মোটামোটি এরকম থাকার কথাঃ

    from django.shortcuts import render, HttpResponse
    
    # Create your views here.
    def index(request):
        return render(request, 'index.html')


উক্ত ভিউটা রেসপন্স হিসেবে শুধুমাত্র ‘index.html’ ফাইলটা রেন্ডার করে পাঠিয়ে দিচ্ছে! অন্য কোন কাজ করছেনা। এটাকে আমাদের মন মত কাজ করাতে প্রথমে models.py থেকে Message মডেলটা ইম্পোর্ট করুনঃ

from models import Message
তারপর index ভিউ ফাংশন এর ভিতর মডেল ম্যানেজার ব্যবহার করে Message এর সবগুলো ফিল্ড একটা ভ্যারিয়েবলে রাখুনঃ

    all_messages = Message.objects.all()

সবগুলো মেসেজ এখন all_messages ভেরিয়েবলে আছে।

সর্বশেষ যেটা করতে হবে, এই ভেরিয়েবলটাকে ‘index.html’ টেমপ্লেট এর ভিতর পাঠিয়ে দিতে হবে! এর ফলে আমরা টেমপ্লেট থেকেই all_messages এর ভ্যালু এক্সেস করতে পারব।

এটা করার জন্য render() ফাংশন এর তৃতীয় প্যারামিটার হিসেবে একটা ডিকশনারি পাস করতে হবে, যার ‘কী’ হবে একটা স্ট্রিং, এবং ভ্যালু হবে `all_message` ভেরিয়াবল।

    return render(request, 'index.html', {'messages': all_messages})

আমরা ডিকশনারির ‘কী’ হিসেবে ‘messages’ স্ট্রিং দিয়েছি। টেমপ্লেটে আমরা `all_messages` ভেরিয়েবল এক্সেস করার জন্য এই messages ‘কী’ টাকেই ব্যবহার করব!

আমাদের সম্পুর্ন views.py এর কোড দেখতে এরকম হবেঃ

    from django.shortcuts import render, HttpResponse
    
    from models import Message
    
    # Create your views here.
    def index(request):
        all_messages = Message.objects.all()
        return render(request, 'index.html', {'messages': all_messages})


ভিউ এর কাজ শেষ! এখন টেমপ্লেট এর কাজ শুরু করতে হবে, মেসেজ এর সবগুলো অবজেক্ট সহ যে ভেরিয়েবলটা টেমপ্লেটে পাঠানো হল তা সুন্দর ভাবে শো করার জন্য আমাদেরকে টেমপ্লেট ট্যাগ এবং ফিল্টারের সাথে পরিচিত হতে হবে... চলুন তাহলে!  
