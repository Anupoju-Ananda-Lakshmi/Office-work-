1st file 
class Inheritance extends Vehicle {
    String model;
    public void drive(){
        System.out.println("driving : "+model);
    }
    public static void main (String args[]){
        Inheritance i = new Inheritance();
        i.brand = "Maruti";
        i.model = "Suzuki";
        i.start();
        i.drive();
    }
}



2nd file 
public class Vehicle{
    String brand ;
    public void start(){
        System.out.println("vehicle Started : "+brand);
    }
}
