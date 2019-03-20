可见性

        public  protected   private 
内部    yes     yes         yes
外部    yes     no          no

子类: 会拥有父类的所有方法和属性，但是可见域根据继承方式不同，而不同,
子类只能看到父类public和protected的方法和属性，    
public  不改变父类public和protected的可见性 
protected   父类public和protected的财产，在子类均变为protected
private 父类public和protected的财产，在子类均变为private