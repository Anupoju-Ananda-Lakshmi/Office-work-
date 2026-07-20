class MethodOverloading{

    public static int calculate(int a,int b){
        int c = a+b;
        return c;
    }

    public static int calculate(int a, int b, int c){
        int d = a*b*c;
        return d;
    }

    public static boolean calculate(int a, int b, int c, int d){
        int ab = a+b;
        int cd = c+d;
        if(ab==cd){
            return true;
        }
        return false;
    }

    public static void main (String args[]){
        int a = calculate(1,2);
        int b = calculate(1,4,2);
        boolean c = calculate(1,6,3,4);
        
        System.out.println("a>>>    "+a);
        System.out.println("b>>>    "+b);
        System.out.println("c>>>    "+c);
    }
}
