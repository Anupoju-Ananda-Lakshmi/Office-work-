1)class Animal {
    void eat(){
        System.out.println("eating");
    }
    String name;
}

public class Dog extends Animal{
    void bark(){
        System.out.println("barking");
    }
    public static void main(String[] args) {
        Dog obj = new Dog();
        obj.bark();
        obj.eat();
    }
}


2)class Person{
    String name;
    void displayName(){
        System.out.println("name :: "+name);
    }
}
class Employee extends Person{
    int employeeId;
    void displayEmployeeId(){
        System.out.println("employeeId is:: "+employeeId);
    }
}

public class Developer extends Employee{
    String programmingLanguage;
    void writeCode(){
        System.out.println("Writing code in "+programmingLanguage);
    }

    public static void main(String[] args) {
        Developer obj = new Developer();
        obj.name ="Andy";
        obj.displayName();
        obj.employeeId = 12345;
        obj.displayEmployeeId();
        obj.programmingLanguage = "JAVA";
        obj.writeCode();
    }
}
i got runtime error ->Exception in thread "main" java.lang.IllegalAccessError: class Developer tried to access private field Employee.name (Developer and Employee are in unnamed module of loader 'app')
        at Developer.main(Developer.java:22)


3)
class Parent {

    int x = 10;
}

public class Child extends Parent {

     int x = 20;
    
     public static void main(String[] args) {
        Child obj = new Child();
        System.out.println(obj.x);
        System.out.println(super.x);
     }
} 

i got compile time error ->non-static variable super cannot be referenced from a static context



4)
A
B
C


5)
i cannot tell because you only told me 
Child obj - new Child ();

you didnt teach mixing up of objects 
