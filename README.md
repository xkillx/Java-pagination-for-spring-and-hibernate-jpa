# Java-pegination-for-spring-and-hibernate-jpa
A helper java class to add a pagination to jsp page. 

## USAGE
Add Pagination.class file to your project.
Get your database table rows counts from your DAO. Example :
    public long counts(){
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


