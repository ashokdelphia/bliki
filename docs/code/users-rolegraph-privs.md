# A Users, Roles & Privileges Scheme Using Graphs

The basic elements:

* Every agent that can interact with a system is represented by a **user**.
* Every capability the system has is authorized by a distinct **privilege**.
* Each user has a list of zero or more **roles**.
    * Roles can **imply** further roles. This relationship is transitive: if role A implies role B, then a member of role A is a member of role B; if role B also implies role C, then a member of role A is also a member of role C. It helps if the resulting role graph is acyclic, but it's not necessary.
    * Roles can **grant** privileges.

A user's privileges are the union of the privileges granted by the transitive closure of their roles.

```sql
create table "user" (
    username varchar
        primary key
    -- credentials &c
);

create table role (
    name varchar
        primary key
);

create table role_member (
    role varchar
        not null
        references role,
    member varchar
        not null
        references "user",
    primary key (role, member)
);

create table role_implies (
    role varchar
        not null
        references role,
    implied_role varchar
        not null
);

create table privilege (
    privilege varchar
        primary key
);

create table role_grants (
    role varchar
        not null
        references role,
    privilege varchar
        not null
        references privilege,
    primary key (role, privilege)
);
```

If your database supports recursive CTEs, this schema can be queried in one shot, since we can have the database do all the graph-walking along roles:

```sql
with recursive user_roles (role) AS (
    select
        role
    from
        role_member
    where
        member = 'SOME USERNAME'
    union
    select
        implied_role as role
    from
        user_roles
        join role_implies on
            user_roles.role = role_implies.role
)
select distinct
    role_grants.privilege as privilege
from
    user_roles
    join role_grants on
        user_roles.role = role_grants.role
order by privilege;
```

If not, you'll need to pull the entire graph into memory and manipulate it there: this schema doesn't give you any easy handles to identify only the roles transitively included in the role of interest, and repeatedly querying for each step of the graph requires an IO roundtrip at each step, burning whole milliseconds along the way.

Realistic use cases should have fairly simple graphs: elemental privileges are grouped into concrete roles, which are in turn grouped into abstracted roles (by department, for example), which are in turn granted to users. If the average user is in tens of roles and has hundreds of privileges, the entire dataset fits in memory, and PostgreSQL performs well. In PostgreSQL, the above schema handles ~10k privileges and ~10k roles with randomly-generated graph relationships in around 100ms on my laptop, which is pretty slow but not intolerable. Perverse cases (interconnected total subgraphs, deeply-nested linear graphs) can take absurd time but do not reflect any likely permissions scheme.
