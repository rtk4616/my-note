## Programming paradigm
程式設計範型：寫程式時，需遵循的一個風格或是思維方式。

影片先介紹我們之前寫過的程式都屬於：
## Procedural Programmming

```rb
# Q：計算車子走了多遠？
# data
car1_name = "A"
car2_name = "B"
car1_speed = 50
car2_speed = 60

# function(method)
def drive(car_name, car_speed, hour)
  distance = car_speed * hour

  puts "#{car_name} runs #{distance}"
end

# 執行程序
drive(car1_name, car1_speed, 10)
drive(car2_name, car2_speed, 20)
```

---

再介紹物件導向`OOP`來跟`PP`比較
## Object-oriented programming

以物件導向思考整件事:
* 思考的中心是怎麼樣定義物件以及物件間怎麼互動

```rb
# 計算車子走多遠
# 會以CAR爲中心來思考
# 每一臺車都會有名字有速度(設定物件)
class Car
  attr_accessor :name, :speed

  def initialize(name, speed)
    @name = name
    @speed = speed
  end

  def drive(hour)
    distance = speed * hour
    puts "#{name} runs #{distance}"
  end
end

# 創造車子(物件)
car1 = Car.new("A", 50)
car2 = Car.new("B", 60)

# 執行物件內的方法(函數)
car1.drive
car2.drive
```

## 物件導向的優勢
* Object 可重複利用，減少程式撰寫時間：
```rb
car3 = Car.new("C", 87) #(Car_Name, Car_Speed)
car4 = Car.new("Object", 88)
```

* 修改彈性高(改汽車{物件})工廠就好)，容易維護
* 將真實世界看成是 objects 的互動，較能幫助聯想
