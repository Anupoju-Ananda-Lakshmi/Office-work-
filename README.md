1)class Calculator{
    void add(int a, int b){
        int res=a+b;
        System.out.println("result of 1st method is "+res);
    }
    void add(int a, int b,int c){
        int res=a+b+c;
        System.out.println("result of 2nd method is "+res);
    }
    void add(double a , double b){
        double res=a+b;
        System.out.println("result of 3rd method is "+res);
    }

    public static void main(String[] args) {
        Calculator cal = new Calculator();
        cal.add(10,20);
        cal.add(10,20,30);
        cal.add(1,2);
    }
}


2)


3)
class Animal1{
    void sound(){
        System.out.println("Animal sound");
    }
}

public class Dog1 extends Animal1{
    
    @Override
    void sound(){
        System.out.println("Dog barks");
    }
    public static void main(String[] args) {
        Dog1 obj = new Dog1();
        obj.sound();
    }
}


4)
class Animal2{
    void sound(){
        System.out.println("Animal sound");
    }
}

public class Dog2 extends Animal2{
    
    @Override
    void sound(){
        System.out.println("Dog barks");
    }
    public static void main(String[] args) {
        Animal2 obj= new Dog2();
        obj.sound();
    }
}
here the reference type is Animal2
Actual object type is Dog2
Dog2 sound() executed
because the reference type is pointing to the Dog2 object so on runtime jvm looks the object it is pointing out is Dog2 and executed the sound()method overridden in Dog2 class 

5)
A-- compiles and executes the mthods in Dog objct
B-- compiles and prints method is dog object
C-- compilation error


6)
it prints the method in child class
