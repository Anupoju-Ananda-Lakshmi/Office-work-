class A{
    void task(){
        System.out.println("Hi this is class A");
    }
}

class B extends A{
    @Override
    void task(){
        System.out.println("Hi this is class B");
    }
}

public class RuntimePolymorphism {
    public static void main(String[] args) {
        //A is reference type and B is the object it is pointing it to 
        //which method is allowed to execute is decided at compile time
        //which method will be executed will be decided at runtime 
        A obj = new B();
        obj.task();
    }
}
