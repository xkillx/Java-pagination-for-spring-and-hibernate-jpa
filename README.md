# Java-pagination-for-spring-and-hibernate-jpa
A helper java class to add a pagination to jsp page. 

## USAGE
Add Pagination.class file to your project.
Get your database table rows counts from your DAO. Example :


    public long count(){
        String query = String.format("SELECT count(*) FROM %s table", entityClassName);
        Query jpaQuery = em.createQuery(query);
        return (Long) jpaQuery.getSingleResult();
    }


Then you can use below example to set limit and offset according to page :

    public List pagination(int resultsPerPage,int page){
        String query = String.format("SELECT table FROM %s table", entityClassName);
        Query jpaQuery = em.createQuery(query);
        jpaQuery.setMaxResults(resultsPerPage);
        if(page <= 0)
            jpaQuery.setFirstResult(page * resultsPerPage);
        else
            jpaQuery.setFirstResult((page-1) * resultsPerPage);
        List resultList = jpaQuery.getResultList();
        return resultList;
    }

To get pagination results in your spring controller you can use below example :


    @RequestMapping(value = "/articles", method = RequestMethod.GET)
    public String doGet(@RequestParam(value = "page", defaultValue = "0", required = false) int page, ModelMap model) {
        List articleEntities;
        int count = articleDao.count();
        pagination.setCount(count);
        pagination.setResultsPerPage(10);
        pagination.setCurrentPage(page);
        if(pagination.isPagination()){
            System.out.println("table rows count were enough and could paginate results");
        }else {
            System.out.println("table rows count were not enough and could not paginate results");
        }
        articleEntities = articleDao.pagination(pagination.getResultsPerPage(),pagination.getCurrentPage());
        model.addAttribute("pagination",pagination);
        model.addAttribute("articleEntities", articleEntities);
        return "articles";
    }
    
    
As you can see, we set our rows count using `pagination.setCount()` and we use `pagination.setResultsPerPage(num)` to set amount of results we wish to get in each page where num is the amount. we use `pagination.setCurrentPage(page)` to let pagination know what page we are in and it sets previous of next page if needed.

### exporting results in jsp

This is simple example of possible jsp file. dont forget to add JSTL taglibs and library:

    <c:if test="${pagination.isPagination() == true}">
        <ul class="pagination">
            <li><a href="<c:if test="${pagination.getPreviousPage()!=-1}">?page=${pagination.getPreviousPage()}</c:if>">&laquo;</a></li>
            <c:set var="count" value="1"></c:set>
            <c:forEach begin="1" end="${pagination.getPagesCount()}">
                <li><a href="?page=${count}">${count}</a></li>
                <c:set var="count" value="${count + 1}"></c:set>
            </c:forEach>
            <li><a href="<c:if test="${pagination.getNextPage()!=-1}">?page=${pagination.getNextPage()}</c:if>">&raquo;</a></li>
        </ul>
    </c:if>

Good Luck;
