## 자바빈이 지켜야 할 규약 
* 아규먼트 없는 기본 생성자
  * 리플렉션으로 인스턴스를 만드려면 생성자를 통해 만들어야하는데, 생성자에 아규먼트가 있으면 만들기 번거롭다.
* getter와 setter메서드 이름 규약
* Serializable 인터페이스 구현
  * 객체를 직렬화를 통해 보관을 할 수 있다. 꺼낼 때 역직렬화를 하여 완벽하게 같은 데이터를 가져올 수 있다.  
  * https://atoz-develop.tistory.com/entry/JAVA%EC%9D%98-%EA%B0%9D%EC%B2%B4-%EC%A7%81%EB%A0%AC%ED%99%94Serialization%EC%99%80-JSON-%EC%A7%81%EB%A0%AC%ED%99%94

자바빈 규약을 완벽하게 지킬 필요는 없다.(GUI 는 지켜야함.)  
다만 게터 세터의 네이밍 규약은 지키는 것이 좋음.(JPA, 타임리프, 등. 필드 들의 접근하는 일관적인 방법)

``` java
public class NutritionFacts implements Serializable{
	private int servingSize = -1;
	private int servings = -1;
	private int calories = 0;
	private int fat = 0;
	private int sodium = 0;
	private int carbohydrate = 0;
	private boolean healthy;
	
	public NutritionFacts() {} // 다른 생성자가 없다면 생략해도 기본으로 만들어짐

	public int getServingSize() {
		return servingSize;
	}

	public void setServingSize(int servingSize) {
		this.servingSize = servingSize;
	}

	public int getServings() {
		return servings;
	}

	public void setServings(int servings) {
		this.servings = servings;
	}

	public int getCalories() {
		return calories;
	}

	public void setCalories(int calories) {
		this.calories = calories;
	}

	public int getFat() {
		return fat;
	}

	public void setFat(int fat) {
		this.fat = fat;
	}

	public int getSodium() {
		return sodium;
	}

	public void setSodium(int sodium) {
		this.sodium = sodium;
	}

	public int getCarbohydrate() {
		return carbohydrate;
	}

	public void setCarbohydrate(int carbohydrate) {
		this.carbohydrate = carbohydrate;
	}

	public boolean isHealthy() {
		return healthy;
	}

	public void setHealthy(boolean healthy) {
		this.healthy = healthy;
	}

}

```  
