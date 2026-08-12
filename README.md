abstract class Payment1{
    abstract void pay();
}

class UPI extends Payment1{

    @Override
    public void pay(){
        System.err.println("payment processing through UPI");
    }
}

class CreditCard extends Payment1{

    @Override
    public void pay(){
        System.err.println("payment processing through CreditCard");
    }
}

public class Payment{
    public static void main(String[] args) {
        Payment1 p1 = new UPI();
        Payment1 p2 = new CreditCard();

        p1.pay();
        p2.pay();
    }
}
