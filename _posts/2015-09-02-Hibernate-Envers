## Database Auditing Using Hibernate Envers
The following Hibernate properties trigger Database Auditing.
```
<!-- Hibernate Envers configuration -->
<!-- To turn off auditing, set hibernate.listeners.envers.autoRegister
                to false -->
<!-- <property name="hibernate.listeners.envers.autoRegister" -->
<!--                value="false" /> -->
<!-- org.hibernate.envers.audit_strategy and org.hibernate.envers.audit_strategy_validity_store_revend_timestamp
  are needed for endrevision timestamping of audit records. This is required
  for table partitioning strategy when audit tables grow too large. -->
<prop  key="org.hibernate.envers.audit_strategy">org.hibernate.envers.strategy.ValidityAuditStrategy</prop>
                <prop key="org.hibernate.envers.audit_strategy_validity_store_revend_timestamp">true</prop>
```
Envers Auditing is turned ON by default by the presence of the Hibernate Envers Jar in the classpath.

Entities that need to be audited are marked with @Audited annotation. Any fields that should be skipped can be marked @NotAudited. OneToMany relationships such as from Employer to Employee should be marked @AuditMappedBy(mappedBy="employer"). Nothing needs to be done the other way around in Employee entity. Similarly OneToOne relationships such as from Employer to HeadQuarters are also marked with @AuditMappedBy(mappedBy="employer"). Nothing needs to be done from the ManyToOne side of the relationship from HeadQuarters to Employer.
The custom entity that captures revision information is below. All other audit tables will reference this table for rev #.
```
/**
 * Used by Hibernate Envers to record revision information on all entity objects. 
 */
@Entity
@RevisionEntity(MyRevisionListener.class)
@Table(name="REVINFO")
public class MyRevisionEntity {
                            @Id
                            @GeneratedValue
                            @RevisionNumber
                            @Column(name="REV")
                            private int id;

                            @RevisionTimestamp
                            @Column(name="REVTSTMP")
                            private long timestamp;

                                                private String username;
                                                private String ipaddress;
                                               
                                                @Column(name="USERNAME", length=80)
                                                public String getUsername() {
                                                                        return username;
                                                }

                                                public void setUsername(String username) {
                                                                        this.username = username;
                                                }
                                               
                            public int getId() {
                                return id;
                            }

                            public void setId(int id) {
                                this.id = id;
                            }

                            @Transient
                            public Date getRevisionDate() {
                                return new Date(timestamp);
                            }
                           
                            public long getTimestamp() {
                                return timestamp;
                            }

                            public void setTimestamp(long timestamp) {
                                this.timestamp = timestamp;
                            }
                           
                            @Column(name="IPADDRESS", length=50)
                                                public String getIpaddress() {
                                                                        return ipaddress;
                                                }

                                                public void setIpaddress(String ipaddress) {
                                                                        this.ipaddress = ipaddress;
                                                }

                            public boolean equals(Object o) {
                                if (this == o) return true;
                                if (!(o instanceof MyRevisionEntity)) return false;

                                MyRevisionEntity that = (MyRevisionEntity) o;

                                if (id != that.id) return false;
                                if (timestamp != that.timestamp) return false;

                                return true;
                            }

                            public int hashCode() {
                                int result;
                                result = id;
                                result = 31 * result + (int) (timestamp ^ (timestamp >>> 32));
                                return result;
                            }

                            public String toString() {
                                return "MyRevisionEntity(id = " + id + ", revisionDate = " + DateFormat.getDateTimeInstance().format(getRevisionDate()) + ")";
                            }
}
When hibernate envers is enabled, the listener that intercepts and passes authentication info into the audit tables is below.
/**
 * When Hibernate Envers is enabled, this listener will intercept all entity modifications.
 *
 */
public class MyRevisionListener implements RevisionListener {
                       
                        private static final String ANONYMOUS = "Anonymous";
                        private static final String UNKNOWN = "Unknown";
                       
                        /**
                         * This method is called whenever you attempt to modify an entity.
                         * REVINFO record is populated with username and ipaddress information provided here.
                         * @see org.hibernate.envers.RevisionListener#newRevision(java.lang.Object)
                         */
                        @Override
                        public void newRevision(Object revisionEntity) {
                                                MyRevisionEntity MyRevEntity = (MyRevisionEntity) revisionEntity;

                                                //get principal info
                                                String userName = ANONYMOUS;
                                                String ipAddress = UNKNOWN;
                                               
                                                SecurityContext context = SecurityContextHolder.getContext();
                                                if (context != null) {
                                                                        Authentication auth = context.getAuthentication();
                                                                       
                                                                        if (auth != null && !(auth instanceof AnonymousAuthenticationToken)) {
                                                                                                userName = auth.getName();

                                                                                                //get http session details
                                                                                                if (auth.getDetails() != null && auth.getDetails() instanceof WebAuthenticationDetails) {
                                                                                                                        WebAuthenticationDetails userDetails = (WebAuthenticationDetails) auth.getDetails();
                                                                                                                        ipAddress = userDetails.getRemoteAddress();
                                                                                                }
                                                                        }
                                                }
                                               
                                                //set userinfo in our custom revisionentity object
                                                MyRevEntity.setUsername(userName);
                                                MyRevEntity.setIpaddress(ipAddress);
                        }

}
```
