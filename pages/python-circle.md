```python
# A short and sweet class to understand functionality
import math

class Circle():
	def __init__ (self, radius, centroid):
		self.radius = radius
		self.centroid_x = centroid[0]
		self.centroid_y = centroid[1]
		
	def get_area(self):
		return math.pi * self.radius**2

	def intersects(self, other_circle):
		centroid_dist = math.sqrt((self.centroid_x-other_circle.centroid_x)**2 + (self.centroid_y-other_circle.centroid_y)**2)
		radius_dist = self.radius + other_circle.radius
		return radius_dist >= centroid_dist


obj = Circle(2, (0,1))

print(obj.radius)
print(obj.get_area())

other_circle = Circle(3, (0,6))
print(obj.intersects(other_circle))
```