interface Vehicle{

    void start();
}

class Car implements Vehicle{
    @Override
    public void start(){
        System.out.println("Car started");
    }
}
class Bike implements Vehicle{
    @Override
    public void start(){
        System.out.println("Bike started");
    }
}
public class VehicleMain{
    public static void main(String[] args) {
        Vehicle v1 = new Car();
        Vehicle v2 = new Bike();

        v1.start();
        v2.start();
    }
}
