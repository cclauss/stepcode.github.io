---
title: Libexpress structs
---

*typedef*s
----------

`
typedef struct Statement_ * Alias;
typedef struct Statement_ * Assignment;
typedef struct Statement_ * Case_Statement;
typedef struct Statement_ * Compound_Statement;
typedef struct Statement_ * Conditional;
typedef struct Statement_ * Loop;
typedef struct Statement_ * Procedure_Call;
typedef struct Statement_ * Return_Statement;
typedef struct Statement_ * Statement;

typedef struct Scope_ *     Increment;
typedef struct Scope_ *     Type;
typedef struct Scope_ *     Scope;
typedef struct Scope_ *     Schema;
typedef struct Scope_ *     Express;
typedef struct Scope_ *     Entity;
typedef struct Scope_ *     Procedure;
typedef struct Scope_ *     Function;
typedef struct Scope_ *     Rule;
`

[Statement\_ \*](http://stepcode.org/doxygen/struct_statement__.html)
---------------------------------------------------------------------

`
/* these should probably all be expression types */

struct Statement_ {
    Symbol symbol;  /**< can hold pcall or alias name but otherwise is not used for anything */
    int type;   /**< one of STMT_XXX above */
    /* hey, is there nothing in common beside symbol and private data?? */
    union u_statement {
        struct Alias_     *     alias;
        struct Assignment_   *  assign;
        struct Case_Statement_   *  Case;
        struct Compound_Statement_ * compound;
        struct Conditional_  *  cond;
        struct Loop_      *     loop;
        struct Procedure_Call_   *  proc;
        struct Return_Statement_  * ret;
        /* skip & escape have no data */
    } u;
};
`

### Alias

` struct Alias_ {
    struct Scope_ * scope;
    struct Variable_ * variable;
    Linked_List statements;     /**< list of statements */
};
`

### Assignment

` struct Assignment_ {
    Expression lhs;
    Expression rhs;
};
`

### Case\_Statement

`
struct Case_Statement_ {
    Expression selector;
    Linked_List cases;
};
`

### Compound\_Statement

`
struct Compound_Statement_ {
    Linked_List statements;
};
`

### Conditional

`
struct Conditional_ {
    Expression test;
    Linked_List code;       /**< list of statements */
    Linked_List otherwise;      /**< list of statements */
};
`

### Loop

`
struct Loop_ {
    struct Scope_ * scope;      /**< scope for increment control */
    Expression while_expr;
    Expression until_expr;
    Linked_List statements;     /**< list of statements */
};
`

### Procedure\_Call

`
struct Procedure_Call_ {
    struct Scope_ * procedure;
    Linked_List parameters; /**< list of expressions */
};
`

### Return\_Statement

`
struct Return_Statement_ {
    Expression value;
};
`

Scope\_ \*
----------

`
struct Scope_ {
    Symbol          symbol;
    char            type;       /* see above */
    ClientData      clientData; /**< user may use this for any purpose */
    int             search_id;  /**< key to avoid searching this scope twice */
    Dictionary      symbol_table,enum_table;
    struct Scope_ * superscope;
    union {
        struct Procedure_ * proc;
        struct Function_ * func;
        struct Rule_ * rule;
        struct Entity_ * entity;
        struct Schema_ * schema;
        struct Express_ * express;
        struct Increment_ * incr;
        struct TypeHead_ * type;
        /* no, query owns a scope rather than scope owning a query
         *      struct Query *query;  */
    } u;
    Linked_List where;      /**< optional where clause */
};
`

### Increment

`/** this is an element in the optional Loop scope */
struct Increment_ {
    Expression init;
    Expression end;
    Expression increment;
};`

### Type

`//NOTE - this is TypeHead_, not Type_!
struct TypeHead_ {
    Type head;          /**< if we are a defined type this is who we point to */
    struct TypeBody_ * body;    /**< true type, ignoring defined types */
};` `struct TypeBody_ {
#if 1
    struct TypeHead_ * head;    /**< for debugging only */
#endif
    enum type_enum type;        /**< bits describing this type, int, real, etc */
    struct {
        unsigned unique     : 1;
        unsigned optional   : 1;
        unsigned fixed      : 1;
        unsigned shared     : 1; /**< type is shared */
        unsigned repeat     : 1; /**< expression is a repeat count*/
        unsigned encoded    : 1; /**< encoded string */
    } flags;
    Type base;      /**< underlying base type if any can also contain true type if this type is a type reference */
    Type tag;       /**< optional tag */
    /* a lot of the stuff below can be unionized */
    Expression precision;
    Linked_List list;   /**< used by select_types and composed types, such as for a list of entities in an instance */
    Expression upper;
    Expression lower;
    struct Scope_ * entity;     /**< only used by entity types */
};`

### Schema

`struct Schema_ {
    Linked_List rules;
    Linked_List reflist;
    Linked_List uselist;
    /** \var refdict, usedict
     * dictionarys into which are entered renames for each specific 
     * object specified in a rename clause (even if it uses the same
     * name */
    Dictionary refdict;
    Dictionary usedict;

    Linked_List use_schemas; /**< lists of schemas that are fully use'd
                              * entries can be 0 if schemas weren't found during RENAMEresolve */
     Linked_List ref_schemas; /**< lists of schemas that are fully ref'd
                               * entries can be 0 if schemas weren't found during RENAMEresolve */
};`

### Express

`struct Express_ {
    FILE * file;
    char * filename;
    char * basename; /**< name of file but without directory or .exp suffix */
};`

### Entity

`struct Entity_ {
    Linked_List supertype_symbols;  /**< linked list of original symbols as generated by parser */
    Linked_List supertypes;         /**< linked list of supertypes (as entities) */
    Linked_List subtypes;           /**< simple list of subtypes useful for simple lookups */
    Expression  subtype_expression; /**< DAG of subtypes, with complete information including, OR, AND, and ONEOF */
    Linked_List attributes;         /**< explicit attributes */
    int         inheritance;        /**< total number of attributes inherited from supertypes */
    int         attribute_count;
    Linked_List unique;             /**< list of identifiers that are unique */
    Linked_List instances;          /**< hook for applications */
    int         mark;               /**< usual hack - prevent traversing sub/super graph twice */
    bool        abstract;           /**< is this an abstract supertype? */
    Type        type;               /**< type pointing back to ourself, useful to have when evaluating expressions involving entities */
};`

### Procedure

`/** location of fulltext of algorithm in source file */
struct FullText {
    char * filename;
    unsigned int start, end;
};` `/** 'parameters' are lists of lists of (type) expressions */
struct Procedure_ {
    int pcount; /**< # of parameters */
    int tag_count;  /**< # of different parameter tags */
    Linked_List parameters;
    Linked_List body;
    struct FullText text;
    int builtin;    /**< builtin if true */
};`

### Function

`struct Function_ {
    int pcount; /**< # of parameters */
    int tag_count;  /**< # of different parameter/return value tags */
    Linked_List parameters;
    Linked_List body;
    Type return_type;
    struct FullText text;
    int builtin;    /**< builtin if true */
};`

### Rule

`struct Rule_ {
    Linked_List parameters;
    Linked_List body;
    struct FullText text;
};`

[Category:Code discussion](Category:Code discussion "wikilink")