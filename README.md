 class Mobile {
    String brand;
    String model;
    long price;

    public void call() {
        System.out.println("calling a brand"+brand);
        System.out.println("calling a model"+model);
        System.out.println("calling a price"+price);
    }
}

public class SampleObj {
    public static void main (String args[]) {
Mobile m1 = new Mobile();
    m1.brand = "Samsung"; 
    m1.model ="S12";
    //m1.price = 42000L;
    m1.call(); 

    Mobile m2 = new Mobile();
    m2.brand ="IPhone" ; 
    m2.model ="Iphone 17 Pro max";
    m2.price = 142000;
    m2.call(); 
    }
}
