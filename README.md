class Person{
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



class Parent {

    int x = 10;
}

public class Child extends Parent {

     int x = 20;
     void display(){
        System.out.println(x);
        System.out.println(super.x);
     }
    
     public static void main(String[] args) {
        Child obj = new Child();
        obj.display();
    }
} 
