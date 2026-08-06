 
 class ParentClass{
     void calculateTotal(){
        System.out.println("calculating total of a student");
    }
    void calculatePercentage(){
        System.out.println("calculating percentage of a student");
    }
}

public class Inheritance extends ParentClass {
    Inheritance inheritance = new Inheritance();
    inheritance.calculateTotal();
    inheritance.calculatePercentage();
    }

   Syntax error on token ".", { expected, package inheritance does not exist, <identifier> expected
