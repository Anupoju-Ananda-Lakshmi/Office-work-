class Employee{
    private double salary;
    private int id;
    private String name;

    public void setSalary(double salary){
        if(salary>=80000){
        this.salary=salary;    
        }
        
    }
        public void setId(int id){
        this.id=id;
    }
        public void setName(String name){
        this.name=name;
    }

     public double getSalary(){
        return salary;
    }

    public int getId(){
        return id;
    }

     public String getName(){
        return name;
    }

}

class Encapsulation{
    public static void main (String args[]){
        Employee e = new Employee();
        e.setId(123);
        e.setName("Andy");
        e.setSalary(70000d);
        System.out.println(e.getId());
        System.out.println(e.getName());
        System.out.println(e.getSalary());

    }
}
