# YouToube
YouTube Tracker
# Application-ის გაშვება
* პროექტის გაშვება: gradle bootRun
* აპლიკაციის გაშვების მისამართი: http://localhost:8686/index.html

# მონაცემების შენახვა

* მონაცემების შენახვისთვის ვიყენებ ფაილებს (users.txt და user_data.txt). Context-ში ვქმნი მათ Bean-ებს და ჩაწერა/წაკითხვის დროს ამით ვხელმძღვანელობ.
* users.txt-ში ვინახავ მომხმარებლის პირად ინფორმაციას: username, period, region და პაროლი. ამ თანმიმდევრობით space-ებით გამოყოფილს.
* user_data.txt-ში ვინხავ მომხმარებლის მიერ მითითებუი მონაცემებით დამუშავებულ და დაგენერირებულ Youtube-დან ინფორმაციას. ამ ფაილშიც space-ებითაა გამოყოფილი თითოეული ინფორმაცია. ეს ინფორმაციებია: record_id ანუ ჩანაწერის აიდი (primaryKey-ს როლს ვასრულებინებ უნიკალურობისთვის), მომხმარებელი, youtube-ს link და კომენტარი
# მონაცემების შეცვლა
* თითოეულ ფაილზე მუშაობის დროს მხოლოდ ან ვკითხულობ ან ვწერ. ანუ კონკრეტული მონაკვეთის(პარამეტრის) შეცვლას არ ვაკეთებ.თუ მინდა კონკრეტული მომხმარებლის კონკრეტული პარამეტრის შეცვლა - ფაილში ბოლოდან ვამატებ (append)  ახალ ჩანაწერს უკვე სასურველი მონაცემებით კონკრეტულ მომხმარებელს.
* წაკითხვისას კი ისე მაქვს, რომ კონკრეტულ მომხმარებელზე მხოლოდ მასზე ბოლო ჩანაწერს ვიმახსოვრებ. და ამ მიდგომით ვჭრი კონკრეტული user-ის კონრეტული მონაცემის შეცვლის პრობლემას.


# live რეჟიმში ინფორმაციის მიწოდება და + ძველი ისტორიის წამოღება
* იმისათვის, რომ live რეჟიმში რაიმე ინფორმაცია წამომეღო გადავწყვიტე გამომეყენებინა socket კავშირი. რაც საშუალებას მომცემდა სასურველ დროს ინფორმაციას მარტივად გავატარებდი ამ არხში. Front-ზე web socket-ის გამოსაყენებლად გამოვიყენე stompClient. ხოლო ისტორიის წამოსაღებად კი 1 HTTP request-ით ვღებულობდი საჭირო მონაცემებს.
* ისტორიას ერთხელ დავხატავდი front-ზე და შემდეგ JavaScript-ის მხარეს ყოველ ახალ მიღებულ პაკეტზე ვამატებდი გამოსახულებას (on Top, Stack-ივით)
* იმისათვის, რომ კონკრეტული ჩანაწერის გამეორება არ მომხდარიყო front-ზე ამისათვის დამჭირდა recordID თითოეულ ჩანაწერზე. UUID-ით ვაგენერირებ თითოეულ id-ს და როდესაც ერთხელ მივიღე მთლიად ძველი ისტორია და ვიცი თითოეული ჩანაწერის id, მაშინ მარტივად დავადგენ socket-ში შემოსული პაკეტი გამორებული ჩანაწერია თუ ახალი. თუ ახალია ვხატავ თუ არადა ვაიგნორებ. ცხადია, ეს დამთხვევა იშვიათად შიძლება მომხდარიყო მაგრამ, თეორიულად სავსებით შესაძლებელია მისი მოხდენა და სწორედ ამიტომ გადავწყვიტზე ID უნიკალურობა გამომეყენებინა იმისათვის, რომ იდენტიური ჩანაწერი (Job-ის მუშაობის დროს დაგენერირებული ზუსტად იგივე ჩანაწერი და არა უბრალოდ მისი იდენტური (მნიშვნელობით)) არ გამომეტანა მომხმარებელთან ვიზუალში.


# Flow
* ვიყენებ Spring Framework-ს და Gradle-ს. აპლიკაციის დაწყების შემდეგ ფაილიდან ვკითხულობ მიმდინარე მომხმარებლებს და ვსტარტავ თითოეულისთვის Job-ს.
* SimpleBroker /topic ზე ვუშვებ და endPoint-ს კი /ws ვსეტავ როგორც websocket-ს
* მაქვს შექმნილი MainScheduler რომლის დავალებაც არის რომ გაუშვას ნაკადებში. მისი პერიოდულობა არის წამის მეათედი Tick-ი უხეშად რომ ვთქვათ და იგი უყურებს სხვა გაშვებულ ნაკადებს და მოქმედებს ისე როგორც თითოეულ ნაკადს აქვს მინიჭებული პარამეტრები.

