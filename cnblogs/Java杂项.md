* 输出Exception时，要用System.out.println(ex);或者ex.printStackTrace();
    * System.out.println(ex.getStackTrace());不行，输出的仅仅是类名，因为没有自行实现ToString()？