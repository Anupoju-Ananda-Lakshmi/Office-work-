public class MethodOverriding extends Vehicle{

    @Override
    public static void statementPrinting(String text){
        System.out.println("Hi this is Runtimepolymorphism in child class  "+text);
    }

    public static void main (String args[]){
        statementPrinting("Andy");
    }
}


public class Vehicle{
    String brand ;
    public void start(){
        System.out.println("vehicle Started : "+brand);
    }

    public static void statementPrinting(String text){
        System.out.println("text that was about to print in parent class is >>.."+text);
    }
}
