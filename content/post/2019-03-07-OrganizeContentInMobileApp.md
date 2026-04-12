---
title: Three Ways to Organize Contents in a Mobile App
date: 2019-03-07 11:22:45
tags: 
- Front End
- Full Stack
categories: 
- Full Stack
---

List-Detail View and Cards are common modes for show content pages for mobile websites & apps. I summarized 3 forms in different types that are I have used in my projects.

### Standard ListView & DetailView

This is a basic mode that can be used in a variety of situations, I used it in a news app of a Chinese customer. Because the news usually contains a lot of text and image, using a separate detailview page is a simple way. In this case, I send the news ID from the news list to the detail page when user click the list item, and the detail page get the news content when it is showing.

[![listview1.gif](https://i.postimg.cc/VkfWKm51/listview1.gif)](https://postimg.cc/Vdht6yjH)

### Flip Card in ListView

It is an advertising poster app of a Chinese customer, most of the content is on the poster and a little bit of info need to be describe in text. So I decided to use the flip mode in the ListView, improve user experience by reducing features. This app is written by React, I forked the [react-card-flip](https://github.com/AaronCCWong/react-card-flip) component and adapted some codes in my project.

[![listview2.gif](https://i.postimg.cc/XN5LJsTM/listview2.gif)](https://postimg.cc/nMHBSGFT)

### Card view like iOS AppStore

The third is a real estate info app of a US customer. This project started shortly after the new iOS 11 App Store, I really like the style so I tried it with a wonderful component  [Cards](https://github.com/PaoloCuscela/Cards). unfortunately, this project was canceled by customer, the screenshot is an early prototype, and this component is still not fully applicable in iOS 12.

[![listview3.gif](https://i.postimg.cc/Ss2rcdRM/listview3.gif)](https://postimg.cc/G8RGCkkL)

### Conclusion

There are more personalized experiences in web and mobile apps than web pages. And Cards are the new creative concept what can improve the UX aspect. Using cards would create more interesting interaction methods, so card-style design is increasing in popularity. 

There is more info about [Card-Based Design](http://babich.biz/using-card-based-design-to-enhance-ux/) can be used as a guide.

