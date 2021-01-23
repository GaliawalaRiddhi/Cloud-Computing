# Cloud-Computing

	properties payara server 
pom.xml - change 1.8 or 8

//persistant unit
jdbc url : jdbc:mysql://localhost:3306/dbname
entity class from db - @xmlrootelement

//jsf cdi bean customerEJB

  EntityManager em;
    Collection<CustomerMaster> customers; //getter setter
public customerEJB() {
        em=Persistence.createEntityManagerFactory("exampu").createEntityManager();
    }

    public Collection<CustomerMaster> getCustomers() {
        customers = em.createNamedQuery("CustomerMaster.findAll").getResultList();
        return customers;
    }

//resource service

@Path("/customer")
public class CustomerResource {

    @Inject customerEJB customerejb;
    
    @GET
    @Produces(MediaType.APPLICATION_JSON)
    public Collection<CustomerMaster> getCustomers() {
        return customerejb.getCustomers();
    }  
}

//servlet

@Inject customerEJB customerejb;
    Collection<CustomerMaster> customers;
    /**

customers = customerejb.getCustomers();
            for (CustomerMaster customer : customers) {
                out.println("Customer Name : " + customer.getName());
                out.println("</br>");
                out.println("Customer PhoneNumber : " + customer.getPhonenumber());
            }

client -project from archtype
pom.xml
payra

//customer class
    public Integer customer_id,rating;
    public String email,name,address,phonenumber;
getter setter

//java interface customer client
 */
@RegisterRestClient(baseUri = "http://localhost:8080/internalexam/rest/customer")
@ApplicationScoped
public interface customerClient {
    
    @GET
    @Produces(MediaType.APPLICATION_JSON)
    Collection<customer> getCustomers();
}

//sevlet
 @Inject @RestClient customerClient cclient;
     Collection<customerClass> customers;

 customers = cclient.getCustomers();
            for (customer c : customers) {
                out.println("Customer Name : " + c.getName());
                out.println("Customer Address : " + c.getAddress());
                out.println("Customer Rating : " + c.getRating());
            }
--jwt


Boostrap
@LoginConfig(authMethod = "MP-JWT")
@DeclareRoles({"Admin","User"})

//Roll allow in resorce
 @Inject employeeEJB employeejb;
    @RolesAllowed("Admin")
    @GET
    @Produces(MediaType.APPLICATION_JSON)
    public Collection<EmpMaster> getemployyes()
    {
        return employeejb.getEmps();
    }
    
    @RolesAllowed("User")
    @GET
    @Path("user")
    @Produces(MediaType.APPLICATION_JSON)
    public String getHello() {
        return "Hello World!!!";
    } 

//public key other src in resources

//properties file search in new and others
 payara-mp-jwt.properties

accepted.issuer=https://server.example.com

//client private key 
// source pkg - token folder copy
//remove myprojects.credential generate token
//generate token file
public static String generateJWT() {
          groups = new ArrayList<>();
          groups.add("Admin");
          groups.add("User");
          subject = "Viraj Jhaveri";


//dependacy in pom.xml
<dependency>
     <groupId>io.vertx</groupId>
     <artifactId>vertx-auth-jwt</artifactId>
     <version>3.8.1</version>
</dependency>

//change in customerclient in client application 

8080 or 8081
//@RegisterRestClient(baseUri = "http://localhost:8080/customer_ms/rest/customer")
@ClientHeaderParam(name = "authorization",value = "{generateToken}")
public interface customerclient {
    @GET
    @Path("/admin")
    @Produces(MediaType.APPLICATION_JSON)
    Collection<employeeClass> getemployyes();
    
    @GET
    @Path("/user")
    @Produces(MediaType.APPLICATION_JSON)
    String getHello();
    
     default String generateToken(){
        String token = GenerateToken.generateJWT();
        return "Bearer "+token; 
     }
}

//user servlet
@Inject @RestClient customerclient cclient;
cclient.getHello() 




-- query using condition

MS Application code 



-> In model - in customerejb.java

public Collection<CustomerMaster> getCustomersWithRatings(String condition, int rating) {
        String rate = String.valueOf(rating);
        System.out.println(rate);
        //availableProducts =  em.createQuery("SELECT p from Product p WHERE p.productid IN (" + allids + ")").getResultList();
        customers = em.createQuery("SELECT c FROM CustomerMaster c WHERE c.rating " + condition + " " + rating).getResultList();
        return customers;
    }

->in service in - customeresource.java

@Inject customerejb ejb;

    @GET
    @Path("getCustomer/{condition}/{rating}")
    @Produces(MediaType.APPLICATION_JSON)
    public Collection<CustomerMaster> getCustomersWithRatings(@PathParam("condition") String condition,@PathParam("rating") int rating){
        return ejb.getCustomersWithRatings(condition, rating);
    }


Client Application Code

-- in customerclient.java

@GET
    @Path("getCustomer/{condition}/{rating}")
    @Produces(MediaType.APPLICATION_JSON)
    Collection<Customer> getCustomersWithRatings(@PathParam("condition") String condition,@PathParam("rating") int rating);
 

-- in servlet 

protected void processRequest(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        try (PrintWriter out = response.getWriter()) {
            /* TODO output your page here. You may use following sample code. */
            out.println("<!DOCTYPE html>");
            out.println("<html>");
            out.println("<head>");
            out.println("<title>Servlet CustomerRating</title>");            
            out.println("</head>");
            out.println("<body>");
            out.println("<h1>Servlet CustomerRating at " + request.getContextPath() + "</h1>");
            out.println("<form method='post'>");
            out.println("condition:<select name='ratingCondition'>"
                    + "<option value='<'>lessThan</option>"
                    + "<option value='>'>greaterThan</option>"
                    + "<option value='<='>lessThanEqualTo</option>"
                    + "<option value='>='>greaterThanEqualTo</option>"
                    + "<option value='='>EqualTo</option>"
                    + "</select><br/>");
            out.println("rating:<input type='number' name='rating' min='1' max='10'><br/>");
            out.println("<input type='submit' value='GET'><br/>");
            out.println("</form>");

            out.println("</body>");
            out.println("</html>");
            if (request.getParameter("rating") != null) {
                String condition = request.getParameter("ratingCondition");
                int rating = Integer.parseInt(request.getParameter("rating"));
                System.out.println("servlet =" + condition + " " + rating);
                 customers  = client.getCustomersWithRatings(condition, rating);
                System.out.println("in servlet");
                for (Customer c : customers) {
                    out.println("Name = " + c.getName());
                    out.println("<br>");
                    out.println("Email = " + c.getEmail());
                    out.println("<br>");
                    out.println("Address = " + c.getAddress());
                    out.println("<br>");
                    out.println("Phone Number = " + c.getPhone_number());
                    out.println("<br>");
                    out.println("Rating = " + c.getRating());
                    out.println("<br>");
                    out.println("<br>");
                }
            }
               
            out.println("</body>");
            out.println("</html>");
        }
    }   






-- insert

--in ebb
public void AddEmployee(int DeptId, String Name) {
        Employeetb emp = new Employeetb();
        Departmenttb dept = new Departmenttb();
        dept.setId(DeptId);
        emp.setDepartmentId(dept);
        emp.setName(Name);
        em.getTransaction().begin();
        em.persist(emp);
        em.getTransaction().commit();
    }

-- in service/resource
 @POST
    @Produces(MediaType.APPLICATION_JSON)
    @Path("addemployee/{departmentid}/{name}")
    public void addEmployee(@PathParam("departmentid") int departmentid, @PathParam("name") String name) {
        employeeModel.AddEmployee(departmentid, name);
    }
  
-- Client application  

-- in client
@POST
    @Produces(MediaType.APPLICATION_JSON)
    @Path("addemployee/{departmentid}/{name}")
    public void addEmployee(@PathParam("departmentid") int departmentid, @PathParam("name") String name);

-- in servlet
out.println("<form method='post'>");
            out.println("<label>Department</label>");
            out.println("<br/>");
            out.println("<input type='number' name='txtdept' />");
            out.println("<br/>");
            out.println("<label>Name</label>");
            out.println("<br/>");
            out.println("<input type='text' name='txtname' />");
            out.println("<br/>");
            out.println("<input type='submit' value='Submit'>");
            out.println("</form>");
            out.println("<table border='1'>");
            out.println("<tr><td>Id</td>");
            out.println("<td>Department Name</td>");
            out.println("<td>Name</td>");
            
            Collection<Employeetb> employeeList = employeeClient.getAllEmployee();
            
            for (Employeetb emp : employeeList) {
                out.println("<tr><td>" + emp.getId() + "</td>");
                out.println("<td>" + emp.getDepartmentId().getName() + "</td>");
                out.println("<td>" + emp.getName() + "</td></tr>");
            }
            out.println("</table>");


-- login managed bean

@Named(value = "loginManagedBean")
@RequestScoped
public class LoginManagedBean {

     @Inject
    SecurityContext securityContext;

    private String username;
    private String password;
    private String message;
    
    public LoginManagedBean() {
    }

    public SecurityContext getSecurityContext() {
        return securityContext;
    }

    public void setSecurityContext(SecurityContext securityContext) {
        this.securityContext = securityContext;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }
    
     public String login() {
        try {

            HttpServletRequest request = (HttpServletRequest) FacesContext.getCurrentInstance().getExternalContext().getRequest();
            HttpServletResponse response = (HttpServletResponse) FacesContext.getCurrentInstance().getExternalContext().getResponse();
            Credential credential = new UsernamePasswordCredential(username, new Password(password));
            AuthenticationStatus status = securityContext.authenticate(request, response,
                    withParams().credential(credential));

            if (status.equals(SUCCESS)) {
                HttpSession session = request.getSession(true);
                session.setAttribute("username", username);
                session.setAttribute("password", password);

                System.out.println("Login Success!");
                //    System.out.println(securityContext.isCallerInRole("Admin"));
                if (securityContext.isCallerInRole("Admin")) {
                    return "/AddEmployee.xhtml";
                } else if (securityContext.isCallerInRole("User")) {
                    return "/Employee.xhtml";
                }
            } else if (status.equals(SEND_FAILURE)) {
                message = "Either user or password is wrong !!!";
                return "/login.xhtml";
            }
        } catch (Exception e) {
            message = "Out- Either user or login is wrong !!!";
            e.printStackTrace();
        }
        return null;
    }

    public String logout() throws ServletException {
        System.out.println("In Log out");
        HttpServletRequest request = (HttpServletRequest) FacesContext.getCurrentInstance().getExternalContext().getRequest();
        request.logout();

        return "/login.xhtml";
    }
}

